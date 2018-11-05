Traefik setup with docker-compose
=================================

This provides a simple example setup to run traefik
(https://traefik.io) as a reverse proxy for a host with several web
apps in docker containers, run using docker-compose, and legacy apps.

- The trafik container itself runs on the host network to have direct
  access to the required ports instead of running it on a dedicated
  network and forwarding ports. This way, it has immediate access to
  all containers on the hose without setting up special routing
  between container networks created by docker-compose.

- All connections are forced to https. Traefik will handle the
  creation and renewal of certificates. In the example configuration,
  we list the domains for which we request certificates explicitly. As
  an alternative, traefik can automatically request certificates the
  moment a domain gets used by a container.

- Use labels on the application containers to configure proxy
  rules. By default, the connection between traefik and containers are
  disabled. You have to explicitly add the label `traefik.enable=true`
  to your container.

- Legacy (not containerized) applications or applications on different
  physical hosts can be proxied by setting up manual rules in
  `rules`. We recommend to create and edit files separately in
  `rules.edit` to prevent traefik from trying to use incomplete rules
  files during editing.

- When migrating an old site to containerized apps with traefik
  ingress, you can set up a catch-all rule with prioriy 1, as done for
  `frontends.old_root` in `rules.edit/old.toml`. This way, connections
  will get sent to the old site unless you have a new app, with a
  higher priority. Almost anything will have a priority higher than 1,
  since the priority is based on the length of the rule.