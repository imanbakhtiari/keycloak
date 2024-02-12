# keycloak
 Keycloak is an open-source identity and access management (IAM) and SSO(Single Sign_on) solution developed by Red Hat. It provides capabilities for authentication

 ### installation of keycloak using docker

### write a docker-compose.yml file

```bash
FROM quay.io/keycloak/keycloak:23.0.5 as builder

# Enable health and metrics support
ENV KC_HEALTH_ENABLED=true
ENV KC_METRICS_ENABLED=true

# Configure a database vendor
ENV KC_DB=postgres_DB

WORKDIR /opt/keycloak
# for demonstration purposes only, please make sure to use proper certificates in production instead
RUN keytool -genkeypair -storepass password -storetype PKCS12 -keyalg RSA -keysize 2048 -dname "CN=server" -alias server -ext "SAN:c=DNS:HOST_IP,IP:127.0.0.1" -keystore conf/server.keystore
RUN /opt/keycloak/bin/kc.sh build

FROM quay.io/keycloak/keycloak:23.0.5
COPY --from=builder /opt/keycloak/ /opt/keycloak/
# change these values to point to a running postgres instance
ENV KC_DB=postgres_DB
ENV KC_DB_URL=jdbc:postgresql://HOST_IP:5432/db
ENV KC_DB_USERNAME=postgres_USER
ENV KC_DB_PASSWORD=postgres_PASS
ENV KC_HOSTNAME=HOST_IP
ENTRYPOINT ["/opt/keycloak/bin/kc.sh"]
```

### then just run this command

```bash
sudo docker-compose up -d
```
