# Explanation

## What was the bug?

When `oauth2_token` is a dict instead of an `OAuth2Token`, the client skips the refresh and sends the request without an `Authorization` header.

## Why did it happen?

The refresh check in `http_client.py` looked like this:

```python
if not self.oauth2_token or (
    isinstance(self.oauth2_token, OAuth2Token) and self.oauth2_token.expired
):
```

The problem is that a non-empty dict is truthy, so `not self.oauth2_token` is `False`. And `isinstance` also returns `False` because it's a dict, not an `OAuth2Token`. So neither condition fires — no refresh happens, no header gets set.

## Why does your fix solve it?

I changed the condition to:

```python
if not isinstance(self.oauth2_token, OAuth2Token) or self.oauth2_token.expired:
```

Now anything that isn't an `OAuth2Token` (whether it's `None`, a dict, or something else) triggers a refresh. And if it is an `OAuth2Token` but expired, it still refreshes. A valid, unexpired token is left alone.

## One edge case the tests don't cover

The tests don't check what happens if `oauth2_token` is set to some other unexpected type like a `list` or a raw `str`. The fix handles it (it would trigger a refresh), but there's no explicit test for that.
