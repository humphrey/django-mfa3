# django-mfa3

A reusable Django app that handles multi factor authentication via FIDO2 and
TOTP.

## Installation

```
pip install django-mfa3
```

## Usage

1.  Add `'mfa'` to `INSTALLED_APPS`
2.  Use `mfa.views.LoginView` instead of the regular login view
3.  Set `MFA_DOMAIN = 'example.com'` and `MFA_SITE_TITLE = 'My site'`
4.  Register URLs: `path('mfa/', include('mfa.urls', namespace='mfa')`
5.  The included templates are just examples, so you should replace them with your own.
6.  Somewhere in your app, add a link to `'mfa:list'`

## Related projects

django-mfa3 is based on [pyotp](https://github.com/pyauth/pyotp) and
[python-fido2](https://github.com/Yubico/python-fido2). The example frontend
code also uses [cbor-js](https://www.npmjs.com/package/cbor-js) and
[qrious](https://www.npmjs.com/package/qrious).

It is inspired by but not otherwise affiliated with
[django-mfa2](https://github.com/mkalioby/django-mfa2).
A big difference between the two projects is that django-mfa2 supports many
methods, while django-mfa3 only supports FIDO2 and TOTP. U2F was dropped
because it is now superseded by FIDO2. Email and Trusted Devices were dropped
because I felt like they have inferior security properties compared to FIDO2
and TOTP.

django-mfa3 is compatible with
[django-stronghold](https://github.com/mgrouchy/django-stronghold/).

## Security considerations

The actual cryptography is handled by pyotp and python-fido2. This library only
provides the glue code for django. Still, there could be issues in the glue.

A notable attack surface is server state: The authentication consists of three
separate HTTP requests: The regular login, fetching a challenge, and a
response. The server keeps some state in the session across these requests. For
example, the user is temporarily stored in the session until the second factor
authentication is done. The logic for handling this state is not as straight
forward as I would like and there might be issues hidden in there.

The [pyotp README](https://github.com/pyauth/pyotp#readme) mentions some
countermeasures for brute force or replay attacks. Those are planned, but have
not yet been implemented.