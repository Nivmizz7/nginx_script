# nginx_script# Scripts pour Serveur Nginx

Ce dépôt contient des scripts utiles pour la gestion et la configuration d'un serveur web Nginx. Ces scripts peuvent vous aider à automatiser certaines tâches et à améliorer votre flux de travail.

## Table des matières

- [Introduction](#introduction)
- [Scripts disponibles](#scripts-disponibles)
  - [Liste des sites activés](#liste-des-sites-activés)
  - [Autres scripts](#autres-scripts)
- [Utilisation](#utilisation)
- [Contributions](#contributions)
- [Licence](#licence)

## Introduction

Nginx est un serveur web performant et flexible, souvent utilisé pour servir des sites web statiques et comme proxy inverse pour des applications. Les scripts fournis ici visent à simplifier la gestion des configurations et à fournir des informations utiles sur les sites hébergés.

## Scripts disponibles

### Liste des sites activés

Ce script listera tous les sites web activés sur votre serveur Nginx, en affichant des informations telles que le nom de domaine, le chemin du dossier, l'URL et si PHP est configuré.

```bash
#!/bin/bash

# Répertoire des sites Nginx
NGINX_SITES_ENABLED="/etc/nginx/sites-enabled"

# Vérifie si le répertoire existe
if [ ! -d "$NGINX_SITES_ENABLED" ]; then
  echo "Le répertoire $NGINX_SITES_ENABLED n'existe pas."
  exit 1
fi

# Déclaration des tableaux pour stocker les données
declare -a domains
declare -a paths
declare -a urls
declare -a php_statuses

# Remplissage des tableaux avec les informations
for site in "$NGINX_SITES_ENABLED"/*; do
  if [ -f "$site" ]; then
    domain=$(grep -oP '(?<=server_name\s)[^;]+' "$site" | xargs)
    path=$(grep -oP '(?<=root\s)[^;]+' "$site" | xargs)
    url="http://$domain"
    if grep -q "fastcgi_pass" "$site"; then
      php_status="Oui"
    else
      php_status="Non"
    fi

    if [ -n "$domain" ] && [ -n "$path" ]; then
      domains+=("$domain")
      paths+=("$path")
      urls+=("$url")
      php_statuses+=("$php_status")
    fi
  fi
done

# Calcul des largeurs maximales et affichage du tableau
# (Ajouter ici le reste du script comme montré précédemment)
