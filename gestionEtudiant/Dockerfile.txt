# Étape 1 : Utiliser une image de base avec Maven et OpenJDK 17 pour la construction
FROM maven:3.8.1-openjdk-17-slim AS build

# Étape 2 : Définir le répertoire de travail dans le conteneur
WORKDIR /app

# Étape 3 : Copier le fichier pom.xml et télécharger les dépendances Maven
COPY pom.xml /app/
RUN mvn dependency:go-offline -B

# Étape 4 : Copier le code source de l'application dans le conteneur
COPY src /app/src

# Étape 5 : Compiler et construire l'application Spring Boot
RUN mvn clean package -DskipTests

# Étape 6 : Utiliser une image légère avec seulement JRE pour l'exécution
FROM openjdk:17-jre-slim

# Étape 7 : Définir le répertoire de travail dans l'image finale
WORKDIR /app

# Étape 8 : Copier le fichier JAR généré depuis l'image de construction
COPY --from=build /app/target/gestionEtudiant-0.0.1-SNAPSHOT.jar /app/

# Étape 9 : Exposer le port sur lequel l'application écoute (par défaut 8080 pour Spring Boot)
EXPOSE 8080

# Étape 10 : Lancer l'application Spring Boot avec le fichier JAR généré
ENTRYPOINT ["java", "-jar", "/app/gestionEtudiant-0.0.1-SNAPSHOT.jar"]
