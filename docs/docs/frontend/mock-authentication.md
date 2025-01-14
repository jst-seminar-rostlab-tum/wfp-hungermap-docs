# Mock Authentication

Authentication is currently implemented in a very basic way. There are two available roles, `RestrictedUser` and `Admin`, defined in `UserRole.ts`. By default, the user is a `RestrictedUser` and authentication is disabled (meaning the user will never be an admin) unless the `NEXT_PUBLIC_FORECASTS_ENABLED` environment variable is set to `true`.

If the env var is set to true, a switch is displayed in the sidebar, where we can switch to admin mode. In admin mode, the user is are granted admin priviliges and can see:
- subscription topics reserved only for the admin role in the subscription modal
- forecasted FCS and rCSI data (currently only mock data) in the Food Security Trends accordion of the Food Consumption map

## userRole context

This React context handles the mocked authentication logic, but this can also be extended to handle the proper implementation without changing the dependent components. By default, the user's role is set to `RestrictedUser`. Calling the `setUserRole` method with a different role will update the role if the `NEXT_PUBLIC_FORECASTS_ENABLED` env var is set to true. The new value is also saved to the `userRole` cookie, so the choice will be remembered the next time the user loads the site. The other components can use the `userRole` property of the context to get updates of the current role of the user, or the `isAdmin` property, which is true if `userRole === Admin`.

When the proper implementation is implemented, after the user is successfully authenticated, the `setUserRole` method should be called. This should store the JWT in a cookie and the user data in the context's state. When the user log's out, this data has to be cleared from the context and the cookie. The components that have previously used the `userRole` or `isAdmin` properties don't have to be changed.