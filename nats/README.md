# NATS Installation

## Installing NATS via Helm
Default installation of NATS, disables authentication. To enable we first save helm `values`:  
```
    helm repo add nats https://nats-io.github.io/k8s/helm/charts/
    helm show values nats/nats > values.yaml
```
By editing `values.yaml` file, we can enable client authentication. We have 5 types of client authentication 
to choose from:  
- [Token Authentication](https://docs.nats.io/running-a-nats-service/configuration/securing_nats/auth_intro/tokens)
- [Username/ Password credentials](https://docs.nats.io/running-a-nats-service/configuration/securing_nats/auth_intro/username_password)
- [TLS Certificate](https://docs.nats.io/running-a-nats-service/configuration/securing_nats/auth_intro/tls_mutual_auth)
- [NKEY with Challenge](https://docs.nats.io/running-a-nats-service/configuration/securing_nats/auth_intro/nkey_auth)
- [Decentralized JWT Authentication/Authorization](https://docs.nats.io/running-a-nats-service/configuration/securing_nats/auth_intro/jwt)

We enable `NKEY with Challenge` authentication for now. For that, we edit `values.yaml` with:  
```
# Authentication setup
auth:
  enabled: true

  # basic:
  #   noAuthUser:
  #   # List of users that can connect with basic auth,
  #   # that belong to the global account.
  #   users:

  #   defaultPermissions:
  #     publish: ["SANDBOX.*"]
  #     subscribe: ["SANDBOX.>"]

  #   # List of accounts with users that can connect
  #   # using basic auth.
  #   accounts:

  # Reference to the Operator JWT.
  # operatorjwt:
  #   configMap:
  #     name: operator-jwt
  #     key: KO.jwt

  # Token authentication
  # token:

  #NKey authentication
  nkeys:
    users:
      - nkey: "UDZTFDAHFUDJ2DXUPE7EAXTOUO5262YU2GQPPP7WARH6XT5DHUSTSIDC"
      - nkey: "UBGU4RVQNP34NRUQS3XW3JNJCXZN3YKDYFZSVUG4OESLUTZUWN66X4HO"
```
We have added two `nkey`s whose values start with letter `U`. `U` stands for `User`.
We can generate such values with [nk](https://github.com/nats-io/nkeys/tree/master/nk) tool.
First install it with:  
```   
    go install github.com/nats-io/nkeys/nk@latest
```
Now create nkeys with:  
```
    nk -gen user -pubout
```
```
    # output
    SUADLHQTQCZYMJMTEO2DKNGO7YT75XQ4HT5WOB4FMNNUEJKAOI72CJRC3Q
    UDZTFDAHFUDJ2DXUPE7EAXTOUO5262YU2GQPPP7WARH6XT5DHUSTSIDC
```
First string starts with letter `S` which means `Seed`. `Seed` is used by client and should be kept safe.
`User` is a public key and it can be safely shared.

Finally, let's install NATS in an namescape called `secure-nats`:  
```
    helm install snats nats/nats -f nats-values.yaml -n secure-nats
```
