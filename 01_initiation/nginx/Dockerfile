# Branche "stable" de Nginx, variante Alpine (petite, avec wget pour le HEALTHCHECK).
# Pour un build reproductible, épingler aussi le digest : nginx:1.30-alpine@sha256:...
FROM nginx:1.31.6-alpine

# Par défaut cette image :
#  - démarre le processus maître en ROOT (les workers passent en "nginx", uid 101)
#  - écoute sur le port 80
#  - envoie ses journaux sur la sortie Docker

# On retire la configuration par défaut (notre nginx.conf ne l'inclut pas)
RUN rm -f /etc/nginx/conf.d/default.conf

# Copie de la page dans le conteneur
COPY html/ /usr/share/nginx/html/

# Copie de la configuration dans le conteneur
COPY nginx.conf /etc/nginx/nginx.conf

# Exposition du port 80
EXPOSE 80

# HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
#   CMD wget -q -O /dev/null http://127.0.0.1:80/health || exit 1

# --- Étape 3 (exercice de durcissement) : tout le conteneur en non-root ---
# 1) remplacer la ligne "COPY nginx.conf ..." par :
#      COPY nginx.nonroot.conf /etc/nginx/nginx.conf
# 2) remplacer EXPOSE 80 et l'URL du HEALTHCHECK par le port 8080
# 3) décommenter :
# USER 101
