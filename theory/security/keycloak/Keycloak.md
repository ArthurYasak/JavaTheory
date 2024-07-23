Start keycloak server on Windows (keycloak bin folder added to Path):

    kc.bat start-dev --http-port 8180

Keycloak flow:

![Keycloak_flow](https://github.com/ArthurYasak/JavaTheory/raw/main/images/Keycloak_flow.png)

![Keycloak_flow_simple_with_front](https://github.com/ArthurYasak/JavaTheory/raw/main/images/Keycloak_flow_simple_with_front.webp)

How to add keycloak in java-project:

In pom.xml:
```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
    </dependency>
    
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-oauth2-client</artifactId>
    </dependency>
    
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
```
In application.yaml: 
```yaml
    server:
      port: 7000
    
    issuer-uri: "http://keycloak:8180/realms/factoring"
    
    spring:
      security:
        oauth2:
          client:
            registration:
              file-verification-service:  # service name in keycloak clients
                client-id: "file-verification-service"
                client-secret: "secret"
                client-name: "file-verification-service"
                provider: "file-verification-service"
                scope:
                  - "openid"
                  - "profile"
                redirect-uri: "http://localhost:${server.port}/login/oauth2/code/file-verification-service"
                client-authentication-method: "client_secret_basic"
                authorization-grant-type: "authorization_code"
            provider:
              file-verification-service:
                issuer-uri: ${issuer-uri}
                token-uri: "http://keycloak:8180/realms/factoring/protocol/openid-connect/token"
                authorization-uri: "http://keycloak:8180/realms/factoring/protocol/openid-connect/auth"
                user-info-uri: "http://keycloak:8180/realms/factoring/protocol/openid-connect/userinfo"
                jwk-set-uri: "http://keycloak:8180/realms/factoring/protocol/openid-connect/certs"
          resourceserver:
            jwt:
              jwk-set-uri: "http://keycloak:8180/realms/factoring/protocol/openid-connect/certs"
              issuer-uri: ${issuer-uri}
```
Configuration class (but in my case server works without this class):
```java
    @EnableWebSecurity
    @Configuration
    public class ClientVerificationSecurityConfig {
    
        @Value("${issuer-uri}")
        private String issuerUrl;
    
        @Bean
        public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
            http
                    .authorizeHttpRequests(authorize -> authorize
                            .requestMatchers("oauth2/**").permitAll()
                            .requestMatchers("/login**").permitAll()
                            .requestMatchers("/error**").permitAll()
                            .anyRequest().authenticated())
                    .oauth2ResourceServer(oauth2 -> oauth2
                            .jwt(Customizer.withDefaults())
                    )
                    .oauth2Login(Customizer.withDefaults())
                    .oauth2Client(Customizer.withDefaults())
            ;
    
            return http.build();
        }
    
        @Bean
        public JwtDecoder jwtDecoder() {
    
            return JwtDecoders.fromIssuerLocation(issuerUrl);
        }
    }
```
Send request with Postman:

![Keycloak_with_postman](https://github.com/ArthurYasak/JavaTheory/blob/main/images/Keycloak_with_postman.png)
![Obtain_access_tokenn](https://github.com/ArthurYasak/JavaTheory/blob/main/images/Obtain_access_token.png)
![Get_token_in_Postman](https://github.com/ArthurYasak/JavaTheory/blob/6564500d91b7e59837b01419adb0faf2cd4c2358/images/Get_token_in_Postman.png)
