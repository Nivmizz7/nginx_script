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

### List of Enabled Sites : '''bash list_sites.sh'''

This script lists all the enabled websites on your Nginx server, displaying information such as the domain name, folder path, URL, and whether PHP is configured.

```bash
#!/bin/bash

# Nginx sites directory
NGINX_SITES_ENABLED="/etc/nginx/sites-enabled"

# Check if the directory exists
if [ ! -d "$NGINX_SITES_ENABLED" ]; then
  echo "The directory $NGINX_SITES_ENABLED does not exist."
  exit 1
fi

# Declare arrays to store data
declare -a domains
declare -a paths
declare -a urls
declare -a php_statuses

# Populate arrays with information
for site in "$NGINX_SITES_ENABLED"/*; do
  if [ -f "$site" ]; then
    domain=$(grep -oP '(?<=server_name\s)[^;]+' "$site" | xargs)
    path=$(grep -oP '(?<=root\s)[^;]+' "$site" | xargs)
    url="http://$domain"
    if grep -q "fastcgi_pass" "$site"; then
      php_status="Yes"
    else
      php_status="No"
    fi

    if [ -n "$domain" ] && [ -n "$path" ]; then
      domains+=("$domain")
      paths+=("$path")
      urls+=("$url")
      php_statuses+=("$php_status")
    fi
  fi
done

# Calculate maximum widths and display the table
# (Add the rest of the script here as shown previously)
