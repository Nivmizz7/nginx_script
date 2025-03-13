# Nginx Server Scripts

This repository contains useful scripts for managing and configuring an Nginx web server. These scripts can help automate certain tasks and improve your workflow.

## Table of Contents

- [Introduction](#introduction)
- [Available Scripts](#available-scripts)
  - [List of Enabled Sites](#list-of-enabled-sites)
  - [Other Scripts](#other-scripts)
- [Usage](#usage)
- [Contributions](#contributions)
- [License](#license)

## Introduction

Nginx is a high-performance and flexible web server often used to serve static websites and as a reverse proxy for applications. The scripts provided here aim to simplify configuration management and provide useful information about hosted sites.

## Available Scripts

### List of Enabled Sites : list_sites.sh

This script lists all the enabled websites on your Nginx server, displaying information such as the domain name, folder path, URL, and whether PHP is configured.

```bash#!/bin/bash

NGINX_SITES_ENABLED="/etc/nginx/sites-enabled"

if [ ! -d "$NGINX_SITES_ENABLED" ]; then
  echo "Le répertoire $NGINX_SITES_ENABLED n'existe pas."
  exit 1
fi

declare -a domains
declare -a paths
declare -a urls
declare -a php_statuses

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

max_domain_length=0
max_path_length=0
max_url_length=0
max_php_length=0

for i in "${!domains[@]}"; do
  (( ${#domains[i]} > max_domain_length )) && max_domain_length=${#domains[i]}
  (( ${#paths[i]} > max_path_length )) && max_path_length=${#paths[i]}
  (( ${#urls[i]} > max_url_length )) && max_url_length=${#urls[i]}
  (( ${#php_statuses[i]} > max_php_length )) && max_php_length=${#php_statuses[i]}
done

max_domain_length=$((max_domain_length + 2))
max_path_length=$((max_path_length + 2))
max_url_length=$((max_url_length + 2))
max_php_length=$((max_php_length + 2))

printf "+-%-${max_domain_length}s-+-%-${max_path_length}s-+-%-${max_url_length}s-+-%-${max_php_length}s-+\n" "-+" "-+" "-+" "-+"
printf "| %-*s | %-*s | %-*s | %-*s |\n" "$max_domain_length" "Nom de domaine" "$max_path_length" "Chemin du dossier" "$max_url_length" "URL" "$max_php_length" "PHP"
printf "+-%-${max_domain_length}s-+-%-${max_path_length}s-+-%-${max_url_length}s-+-%-${max_php_length}s-+\n" "-+" "-+" "-+" "-+"

for i in "${!domains[@]}"; do
  printf "| %-*s | %-*s | %-*s | %-*s |\n" "$max_domain_length" "${domains[i]}" "$max_path_length" "${paths[i]}" "$max_url_length" "${urls[i]}" "$max_php_length" "${php_statuses[i]}"
done

printf "+-%-${max_domain_length}s-+-%-${max_path_length}s-+-%-${max_url_length}s-+-%-${max_php_length}s-+\n" "-+" "-+" "-+" "-+"

printf "Made by Nivmizz7 :)"
echo'''
