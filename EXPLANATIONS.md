# Explanation

## What was the bug?

The client did not refresh the OAuth2 token when `oauth2_token` was a dictionary.  
The refresh logic only handled cases where the token was missing (`None`) or an expired `OAuth2Token` instance. If a dictionary was assigned to `oauth2_token`, the refresh condition was skipped and no Authorization header was added to the request.

## Why did it happen?

The condition that triggered token refresh checked:

- if the token was missing, or
- if the token was an expired `OAuth2Token`.

A dictionary token is truthy and not an `OAuth2Token`, so neither condition triggered a refresh. As a result, the request proceeded without a valid token and the Authorization header was never set.

## Why does your fix solve it?

The fix refreshes the token whenever `oauth2_token` is not a valid `OAuth2Token` instance or when the token is expired. This ensures the client always uses a valid token before attaching the Authorization header.

## Edge case not covered by tests

The tests do not cover a scenario where `refresh_oauth2()` fails (for example, if a network request or token generation error occurs). Handling such failures would require additional error handling and tests.