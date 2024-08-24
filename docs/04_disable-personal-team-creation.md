# Jetstream Personal Teams

## Disable creation of personal teams for each user

-   [Documentation](https://medium.com/devlan-io/disabling-personal-teams-in-laravel-8-and-jetstream-1fd083593e08)

-   edit `app/Actions/Fortify/CreateNewUser.php` and comment out `$this->createTeam($user);` from the create function in line

-   Create `app/Traits/HasNoPersonalTeam.php` with the following contents:

```php
<?php

namespace App\Traits;

trait HasNoPersonalTeam
{

    /**
     * Determine if the user owns the given team.
     *
     * @param  mixed  $team
     * @return bool
     */
    public function ownsTeam($team)
    {
        // return $this->id == $team->user_id;
        return $this->id == optional($team)->user_id;
    }

    /**
     * Determine if the given team is the current team.
     *
     * @param  mixed  $team
     * @return bool
     */
    public function isCurrentTeam($team)
    {
        return optional($team)->id === $this->currentTeam->id;
    }

    /**
     * Determine if the user is apart of any team.
     *
     * @param  mixed  $team
     * @return bool
     */
    public function isMemberOfATeam(): bool
    {
        return (bool) ($this->teams()->count() || $this->ownedTeams()->count());
    }

}
```

-   update the User model `app/Models/User.php` and overwrite two methods `ownsTeam` and `isCurrentTeam` on the Jetstream `HasTeams` trait with the newly created `HasNoPersonalTeam` trait

```php
// ...
use App\Traits\HasNoPersonalTeam;

class User extends Authenticatable
{
    use HasApiTokens;
    use HasFactory;
    use HasProfilePhoto;
    use HasNoPersonalTeam, HasTeams {
        HasNoPersonalTeam::ownsTeam insteadof HasTeams;
        HasNoPersonalTeam::isCurrentTeam insteadof HasTeams;
    }
    use Notifiable;
    use TwoFactorAuthenticatable;

    // ...
}
```

-   create `app/Http/Middleware/EnsureHasTeam.php` to verify that the user has a team and redirect to the jetstream create team page if not.

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class EnsureHasTeam
{
    public function handle(Request $request, Closure $next)
    {
        if (!auth()->user()->isMemberOfATeam()) {
            return redirect()->route('teams.create');
        }
        $this->ensureUserHasCurrentTeamSet();
        return $next($request);
    }

    protected function ensureUserHasCurrentTeamSet(): void
    {
        if (is_null(auth()->user()->current_team_id)) {
            $user = auth()->user();
            $user->current_team_id = $user->allTeams()->first()->id;
            $user->save();
        }
    }
}
```

-   register middleware in `bootstrap/app.php` inside the `->withMiddleware()` function:

```php
$middleware->alias([
    'team' => EnsureHasTeam::class
]);
```

-   in `routes/web.php` add `team`middleware group to `auth:sanctum` middleware group

```php
Route::middleware([
    'auth:sanctum',
    config('jetstream.auth_session'),
    'verified',
])->group(function () {
    Route::get('/dashboard', function () {
        return Inertia::render('Dashboard');
    })->name('dashboard');

    Route::middleware(['team'])->group(function () {
        // Team Routes
    });
});
```

-   in the layout `resources/js/Layouts/AppLayout.vue` update both instances of `v-if="$page.props.jetstream.hasTeamFeatures"` with `v-if="$page.props.jetstream.hasTeamFeatures && $page.props.auth.user.current_team"` so the team dropdown only gets shown if the user has a team
