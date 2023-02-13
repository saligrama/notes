# TLS and WebPKI

Loading a site:

1. Client loads `cnn.com` over HTTPS, sends client hello
2. Server has a certificate chain used for server hello e.g.
    - TBS (to-be-signed) certificate
        - Subject: cn = cnn.com
        - Public Key
        - Expiration
        - Extension
        - Issuer: cn = Let's Encrypt
    - Signature
3. Client has root cert store containing a number of CA's, e.g.
    - Chrome root store
        - Root: Let's Encrypt
            - Multiple intermediate certs
                - Huge number of leaves signed by intermediate cert
