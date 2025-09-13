This is Spring Cloud Config Server, a centralized configuration server for distributed systems. It provides a way to manage external properties for applications across all environments.
## Features
- Centralized configuration management
- Support for multiple backends (Git, SVN, filesystem, etc.)
- Dynamic property updates
- Environment-specific configurations
- Secure configuration storage


# How to access the configuration information
# http://localhost:8888/application/default
# application is the application name corresponding to the config filename without extension.
# default is the profile (or use your profile name if applicable).
# Example: application.yml
# http://localhost:8888/rapido/default
# Example: rapido.yml
# http://localhost:8888/rapido/prod
# Example: rapido-prod.yml
# http://localhost:8888/rapido/dev
# Example: rapido-dev.yml
# http://localhost:8888/rapido/uat
# Example: rapido-uat.yml


# ------------------------------------------------------------------------------
# logging.level.org.springframework.cloud.config.server=DEBUG
# logging.level.org.springframework.cloud.config.server.environment=DEBUG
# logging.level.org.eclipse.jgit=DEBUG

# Below log is obtained when the config server starts up. when we add above logging level. in application.properties of this project
# DEBUG 3652 --- [config-server] [           main] .s.c.c.s.s.GitCredentialsProviderFactory : No credentials provider required for URI https://github.com/takumisamurai/config-repo.git



# http://localhost:8888/application/default
# It fetches application.yml from config-repo.git
# http://localhost:8888/application/dev
# It fetches application-dev.yml and application.yml from config-repo.git
# http://localhost:8888/application/uat
# It fetches application-uat.yml and application.yml from config-repo.git
# http://localhost:8888/application/prod
# It fetches application-prod.yml and application.yml from config-repo.git

# http://localhost:8888/user-service/default  
# It fetches application.yml from config-repo.git
# http://localhost:8888/user-service/dev
# /user-service/dev => fetching user-service-dev.yml, application-dev.yml, and application.yml
# http://localhost:8888/user-service/uat
# /user-service/uat => fetching user-service-uat.yml, application-uat.yml, and application.yml
# http://localhost:8888/user-service/prod
# /user-service/prod => fetching user-service-prod.yml, application-prod.yml, and application.yml


# Spring Cloud Config Server merges configuration files in a specific order of precedence when you request 
# a config for an application and profile, such as http://localhost:8888/user-service/dev. 
# Here’s why it fetches multiple files like application.yml, application-dev.yml, and user-service-dev.yml:

# Order of Property Sources and Precedence Rules
# ------------------------------------------------------------------------------

# application.yml
# Global/shared config applied to all applications regardless of name or profile.

# application-{profile}.yml (e.g., application-dev.yml)
# Global config specific to the Spring profile (dev, prod, etc.), overrides global defaults.

# {application}.yml (e.g., user-service.yml)
# Application-specific config that applies regardless of profile.

# {application}-{profile}.yml (e.g., user-service-dev.yml)
# Most specific config for the given application and profile, highest precedence.

# ------------------------------------------------------------------------------

# Summary of Why Spring Cloud Config Returns These
# When you request configuration for user-service app at dev profile, the config server aggregates config from these files in order of precedence:
# application.yml < application-dev.yml < user-service.yml < user-service-dev.yml
# Properties defined in more specific files override those in more general ones.
# This layered approach allows shared defaults with overrides per profile and per application.

# ------------------------------------------------------------------------------
# Result
# Properties from user-service-dev.yml override any duplicates from application-dev.yml or application.yml. 
# This cascading setup is the default and consistent with Spring's property source handling.
