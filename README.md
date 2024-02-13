# keycloak
 Keycloak is an open-source identity and access management (IAM) and SSO(Single Sign_on) solution developed by Red Hat. It provides capabilities for authentication

 ### installation of keycloak using docker

### write a Dockerfile

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

### and then run 
```bash
cd /path/to/Dockerfile
sudo docker build name:tag .
```
### and then 
### then just make a docker-compose.yml file
```bash
version: '3.8'

services:
  mykeycloak:
    container_name: mykeycloak
    image: kc:v1
    environment:
      - KEYCLOAK_ADMIN=admin
      - KEYCLOAK_ADMIN_PASSWORD=change_me
    ports:
      - "8443:8443"
        # for persist data
        #    volumes:
        #- /opt/keycloak/:/opt/keycloak/data/h2
    restart: always
    command: start --optimized

volumes:
  keycloak:

```

```bash
sudo docker-compose up -d
```
