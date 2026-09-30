# GitHub-Organization

Configuration d'organisation TrysCode pour les workflows GitHub partagés et le
profil public. Le dépôt ne contient pas de secret et ne doit pas exposer de
ressource interne directe.

## Rôle

Centraliser les éléments organisationnels réutilisables sans mélanger
configuration publique, secrets de production et état d'exécution des services.

## Responsabilités

- maintenir le profil d'organisation ;
- documenter les workflows réutilisables ;
- garder la branche `develop` comme branche de construction courante ;
- réserver `main` aux releases ou publications cohérentes.

## Hors périmètre

Le dépôt ne contient pas les secrets GitHub, ne déploie pas directement les
services TrysCode et ne remplace pas les pipelines propres aux repositories.

## Architecture

Les documents de profil vivent dans `profile/`. Les guides de workflows
réutilisables vivent dans `docs/`. Les accès privés doivent passer par Tailscale
ou Services Kubernetes, jamais par une IP privée directe documentée en dur.

## Prérequis

- Git ;
- droits GitHub adaptés au repository d'organisation ;
- accès Tailscale seulement pour consulter une ressource privée hors GitHub.

## Installation

Aucune installation applicative n'est requise.

## Configuration

Les règles d'organisation et workflows doivent rester lisibles, versionnés et
revus avant publication. Les secrets restent gérés par GitHub et ne sont jamais
commités.

## Variables d'environnement

Aucune variable runtime n'est requise dans ce dépôt.

## Commandes

```bash
git status --short --branch
```

Les validations spécifiques doivent rester dans les repositories qui consomment
les workflows.

## Tests

Le contrôle local porte sur la documentation et l'absence de secrets. Les
workflows réutilisables sont validés par les repositories consommateurs.

## API et messages

Aucune API HTTP, queue RabbitMQ ou événement métier n'est exposé par ce dépôt.

## Sécurité

Ne versionner aucun token, secret d'organisation, clé privée, webhook secret ou
endpoint interne direct. Les accès privés documentés doivent utiliser Tailscale
ou un Service Kubernetes nommé.

## Observabilité

L'observabilité des workflows est fournie par GitHub Actions dans les
repositories consommateurs.

## Déploiement

Les changements courants partent sur `develop`; `main` reste réservé aux
publications cohérentes du profil ou des workflows partagés.

## Migrations

Toute modification de workflow réutilisable doit préciser les repositories
impactés, la compatibilité descendante et le rollback.

## Dépannage

Vérifier la branche, l'état Git, les droits GitHub et les journaux Actions des
repositories consommateurs.

## Contribution

Garder les changements atomiques, relire les effets organisationnels et pousser
les travaux courants sur `develop`.

## Licence et statut

Statut interne TrysCode. La licence suit les règles de l'organisation.

## Propriétaire

Baptiste RENNESON BOUTARD / équipe plateforme TrysCode.

## Limitations

Ce dépôt décrit l'organisation et ses workflows partagés. Il ne prouve pas à lui
seul qu'un service applicatif est construit, déployé ou vérifié.
