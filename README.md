# Tariq Tax & Law — plugin Claude

Expertise **fiscale et juridique marocaine** pour Claude (Desktop, Code, Cowork).

Le plugin installe **15 skills méthodologiques** qui guident Claude vers une utilisation
exhaustive et efficiente du connecteur MCP **Tariq Tax & Law** : méthode juridique (socle),
fiscalité d'entreprise, contentieux fiscal, comptabilité (CGNC), avocat, notariat, droit
administratif, civil, commercial, pénal, travail, foncier, expert-comptable, huissier,
professions juridiques.

La **profondeur** — CGI article par article, notes circulaires, décisions de la commission
de recours fiscal, jurisprudence (Cassation / administrative), doctrine, conventions fiscales —
vit dans le serveur MCP, derrière l'abonnement (accès *gated* OAuth + abonnement).

## Installation

**Claude Desktop** — bouton « Installer » sur la page d'abonnement, ou :

```
/plugin marketplace add spoopoo-create/tariq-tax-law-plugin
/plugin install tariq-tax-law@tariq-tax-law-marketplace
```

À la première utilisation du connecteur, Claude ouvre la fenêtre de connexion
(authentification + abonnement).

## Contenu

| | |
|---|---|
| Skills | 15 playbooks d'orchestration (`plugins/tariq-tax-law/skills/`) |
| Connecteur MCP | `https://fiscal.147-93-52-143.nip.io/mcp` (HTTP, gated) |
| Outils | `fiscal_rechercher`, `fiscal_lire`, `fiscal_explorer`, `fiscal_expertise` (+ `search`/`fetch`) |

Les skills ne contiennent aucun contenu réglementaire : elles orchestrent les appels au MCP.
Toute la matière officielle est servie par le connecteur.
