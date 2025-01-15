## Setting Vault in project
```yml
spring:
  config:
    import: vault://my/pass/in/vault # задание пути в Spring Cloud Vault
  cloud:
    vault:
      enabled: true
      uri: https://vaultapi.my.domain
      authentication: APPROLE

      # from VM options/running configurations or environment variables
      approle:
        role-id: ${VAULT_ROLE_ID}
        secret-id: ${VAULT_SECRET_ID}

      # works without it, enabled by default
      config.lifesycle: 
        enabled: false
      session.lifesycle:
        enabled: false

  # задание логина/пароля для доступа к БД или других параметров находящихся по пути my/pass/in/vault
  datasource:
    username: ${VAULT_DB_USER}
    password: ${VAULT_DB_PASS}
```
##### Add to VM options/running configurations or to environment variables:
```
-DVAULT_ROLE_ID: my_role_id
-DVAULT_SECRET_ID: my_secret_id
```
