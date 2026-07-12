# Morning Editorial Operator - Blueprint v0.1

Date: 2026-07-12  
Auteur: ClawdIA  
Statut: specification de creation  
Agent cible: `morning-editorial-operator`

## 1. Intention

Creer un agent OpenClaw dedie a la production editoriale quotidienne de Laurent Serre.

L'agent doit orchestrer le cron du matin a 6h45, de l'analyse strategique du sujet jusqu'au package de publication, avec une montee progressive vers l'auto-publication blog puis LinkedIn.

Decision d'architecture: ne pas fusionner `site-maintainer` et `copywriter-publisher`. Creer un operateur dedie, capable de mobiliser des capacites editoriales, SEO, visuelles et publication, mais avec ses propres regles, logs, gates qualite et limites de production.

## 2. Nom et perimetre

### Identite technique

- Agent id: `morning-editorial-operator`
- Nom lisible: Morning Editorial Operator
- Role: operateur editorial quotidien autonome
- Workspace: `~/.openclaw/workspace-morning-editorial-operator`
- Agent dir: `~/.openclaw/agents/morning-editorial-operator/agent`
- Repo de travail: `~/.openclaw/workspace-morning-editorial-operator/projects/morning-editorial-pipeline`
- Cron cible: tous les jours a 6h45 Europe/Paris

### Mission

Chaque matin, l'agent doit produire un dossier editorial complet:

- analyse des opportunites de sujet;
- choix argumente du sujet du jour;
- brief editorial;
- article de blog;
- proposition de visuel ou BD/carrousel;
- controles qualite;
- package de publication blog;
- brouillon LinkedIn;
- rapport court a Laurent.

### Hors perimetre initial

Pendant la phase de construction et de rodage, l'agent ne doit pas:

- publier automatiquement sur LinkedIn;
- modifier la configuration globale OpenClaw;
- supprimer ou reinitialiser des donnees;
- publier un article si la checklist blog est incomplete;
- choisir un sujet sans justification tracee;
- ignorer un echec de build, de commit ou de verification live.

## 3. Architecture OpenClaw

### Isolation

L'agent doit etre isole comme un vrai operateur specialise:

- workspace dedie;
- instructions systeme propres;
- memoire quotidienne propre;
- allowlist explicite de skills;
- repo de pipeline versionnable;
- logs quotidiens;
- artefacts datables.

### Configuration indicative

```json
{
  "id": "morning-editorial-operator",
  "name": "Morning Editorial Operator",
  "workspace": "~/.openclaw/workspace-morning-editorial-operator",
  "agentDir": "~/.openclaw/agents/morning-editorial-operator/agent",
  "model": {
    "primary": "deepseek/deepseek-v4-flash"
  },
  "skills": [
    "publication-workflow",
    "blog-article-creator",
    "article-publication",
    "bd-carousel",
    "blog-hero-image-creator",
    "seo",
    "serp-analyzer",
    "google-search-console",
    "google-analytics",
    "linkedin-api",
    "gog",
    "openclaw-cli",
    "self-improving-agent"
  ],
  "identity": {
    "name": "Morning Editorial Operator",
    "emoji": "📰"
  }
}
```

Note: la liste exacte des skills doit etre confirmee au moment de la configuration. L'objectif est une allowlist explicite, pas un heritage large.

### Outils autorises

Autoriser uniquement ce qui sert au pipeline:

- lecture/ecriture du workspace dedie;
- lecture du repo blog LaurentSerre.com;
- commandes Git necessaires;
- recherche web/SEO si disponible;
- acces GSC/GA4 si deja configure;
- generation image via le specialiste ou skill approprie;
- envoi de rapport a Laurent;
- cron de l'agent.

### Outils interdits ou encadres

- Pas de reset destructif.
- Pas de suppression massive.
- Pas de modification globale de config OpenClaw hors phase de setup controlee.
- Pas de publication LinkedIn automatique avant validation explicite.
- Pas de publication blog si une verification bloquante echoue.

## 4. Repo cible

Chemin:

```text
~/.openclaw/workspace-morning-editorial-operator/projects/morning-editorial-pipeline/
```

Arborescence recommandee:

```text
morning-editorial-pipeline/
  README.md
  RUNBOOK.md
  QUALITY.md
  CONFIG.md
  CHANGELOG.md
  prompts/
    01_research.md
    02_topic_selection.md
    03_brief.md
    04_article.md
    05_visuals_bd.md
    06_quality_gate.md
    07_publish_package.md
    08_report.md
  templates/
    daily-run-report.md
    topic-scorecard.md
    article-brief.md
    qa-report.md
    linkedin-draft.md
  runs/
    .gitkeep
  scripts/
    run_daily.sh
    validate_package.sh
    collect_context.sh
  docs/
    publication-policy.md
    rollback-policy.md
    linkedin-policy.md
    blog-checklist.md
```

## 5. Fichiers systeme du workspace agent

### `AGENTS.md`

Contenu attendu:

- mission quotidienne;
- ordre strict des etapes;
- regle de dry-run initial;
- criteres de blocage;
- regles de publication;
- format de rapport;
- obligation de logs;
- obligation de ne pas publier si QA incomplete.

### `SOUL.md`

Positionnement:

- operateur editorial rigoureux;
- ton sobre, commercial, utile;
- pas de marketing creux;
- pas de contenu generique;
- priorite a l'expertise terrain de Laurent;
- chaque article doit etre utile a un dirigeant PME B2B.

### `USER.md`

Elements a inclure:

- Laurent Serre;
- audience: dirigeants de PME B2B, managers commerciaux, responsables sales enablement;
- preference: concret, actionnable, structure;
- rejet: banalites, abstraction, remplissage;
- blog: laurentserre.com;
- email self: `ls@laurentserre.com`;
- publication LinkedIn: validation humaine au depart.

### `TOOLS.md`

Elements a inclure:

- chemin canonique du repo blog;
- commandes de build/test du blog;
- checklist de publication blog;
- chemin des images blog;
- procedure de commit/push;
- procedure de verification live;
- procedure LinkedIn en dry-run;
- contact/rapport a Laurent.

### `HEARTBEAT.md`

Court, uniquement pour controles:

- verifier le dernier run 6h45;
- signaler si run absent, bloque ou incomplet;
- ne pas ressusciter d'anciennes taches.

## 6. Pipeline quotidien

Chaque run doit creer un dossier:

```text
runs/YYYY-MM-DD/
  00_context.md
  01_research.md
  02_topic_scorecard.md
  03_selected_topic.md
  04_brief.md
  05_article.md
  06_visuals_plan.md
  07_qa_report.md
  08_publish_package.md
  09_linkedin_draft.md
  10_final_report.md
```

### Etape 0 - Contexte

Collecter:

- date;
- evenements ou contraintes du jour;
- calendrier editorial existant;
- articles recents;
- opportunites SEO;
- signaux GSC/GA4 si disponibles;
- sujets deja traites pour eviter la repetition.

Sortie: `00_context.md`.

### Etape 1 - Recherche

Identifier 5 a 10 opportunites editoriales.

Sources possibles:

- Search Console;
- Analytics;
- SERP;
- tendances sectorielles;
- questions clients recurrentes;
- anciens contenus a renforcer;
- opportunites GEO/AEO.

Sortie: `01_research.md`.

### Etape 2 - Scoring sujet

Chaque sujet recoit un score:

- potentiel business;
- utilite pour dirigeant PME;
- pertinence SEO;
- originalite;
- evidence terrain disponible;
- facilite de production le jour meme;
- potentiel LinkedIn;
- compatibilite avec les offres Laurent Serre.

Sortie: `02_topic_scorecard.md`.

### Etape 3 - Selection

Choisir un seul sujet principal.

La selection doit expliquer:

- pourquoi ce sujet maintenant;
- quelle cible precise;
- quel angle;
- quel risque editorial;
- ce qui rend l'article non generique.

Sortie: `03_selected_topic.md`.

### Etape 4 - Brief

Produire un brief avant redaction:

- titre provisoire;
- promesse lecteur;
- audience;
- intention de recherche;
- plan H2/H3;
- these centrale;
- exemples terrain;
- CTA;
- liens internes pressentis;
- visuel ou BD associee.

Sortie: `04_brief.md`.

### Etape 5 - Article

Rediger l'article selon les standards du blog:

- expertise commerciale terrain;
- structure claire;
- exemples concrets;
- densite utile;
- SEO propre;
- CTA coherent;
- pas de contenu creux;
- pas d'effet "article IA".

Sortie: `05_article.md`.

### Etape 6 - Visuels / BD

Produire au minimum un plan visuel:

- image hero ou BD/carrousel;
- brief image;
- style compatible Laurent Serre;
- usage blog et LinkedIn;
- alt text;
- nom de fichier propose.

En phase 1, le visuel peut rester au stade de prompt ou draft. En production, il doit etre genere et integre.

Sortie: `06_visuals_plan.md`.

### Etape 7 - Quality gate

Checklist bloquante:

- sujet justifie;
- brief present;
- article complet;
- titre et meta;
- liens internes;
- CTA;
- image ou plan image;
- absence de repetition evidente avec articles recents;
- conformite ton Laurent;
- build/test si publication;
- index blog mis a jour si article cree;
- dashboard editorial mis a jour si necessaire;
- commit propre si modification repo;
- verification live si deploiement.

Sortie: `07_qa_report.md`.

### Etape 8 - Package publication

Preparer:

- chemin cible article;
- slug;
- fichiers a creer/modifier;
- image;
- entry blog index;
- note dashboard editorial;
- commande de test;
- commande de commit;
- statut publication.

Sortie: `08_publish_package.md`.

### Etape 9 - Brouillon LinkedIn

Produire un post LinkedIn en brouillon:

- accroche terrain;
- idee forte;
- mini-histoire ou tension business;
- lien article;
- appel a discussion;
- pas de publication automatique en phase 1 et 2.

Sortie: `09_linkedin_draft.md`.

### Etape 10 - Rapport

Rapport court:

- sujet choisi;
- statut;
- fichiers produits;
- blocages;
- decision attendue;
- prochain run.

Sortie: `10_final_report.md`.

## 7. Gates de production

### Phase A - Construction

Objectif: agent configuré et lançable.

Critere de sortie:

- workspace existe;
- repo existe;
- fichiers systeme crees;
- premier run manuel possible;
- aucun cron actif en publication live.

### Phase B - Dry-run manuel

Objectif: valider le comportement sans cron.

Critere de sortie:

- 3 runs manuels complets;
- artefacts crees dans `runs/YYYY-MM-DD/`;
- rapport final exploitable;
- corrections integrees dans instructions.

### Phase C - Cron dry-run

Objectif: verifier la stabilite quotidienne.

Regle:

- cron 6h45 actif;
- aucune publication blog;
- aucune publication LinkedIn;
- package + rapport seulement.

Critere de sortie:

- 5 runs consecutifs exploitables;
- pas d'echec silencieux;
- pas d'artefact manquant critique.

### Phase D - Blog semi-auto

Objectif: autoriser la publication blog sous condition.

Regle:

- publication blog uniquement si QA OK;
- build OK;
- blog index mis a jour;
- image presente;
- commit propre;
- verification live;
- rapport envoye.

Critere de sortie:

- 3 publications blog propres;
- aucun oubli de l'index blog;
- aucun echec de verification live.

### Phase E - LinkedIn controle

Objectif: passer LinkedIn de brouillon a publication encadree.

Regle:

- 7 brouillons valides en historique;
- format stable;
- publication automatique seulement apres feu vert explicite;
- possibilite de rester en mode validation humaine.

## 8. Loop de creation jusqu'au bout

Cette boucle est la procedure operative pour creer l'agent.

### Cycle 1 - Specification

Responsable: ClawdIA

Actions:

- produire ce blueprint;
- identifier fichiers a generer;
- definir gates;
- lister questions ouvertes.

Sortie attendue:

- `morning-editorial-operator-v0.1.md`.

### Cycle 2 - Scaffold

Responsable: ClawdIA ou agent dev

Actions:

- creer workspace;
- creer agentDir;
- creer repo pipeline;
- initialiser fichiers systeme;
- initialiser arborescence repo;
- preparer config OpenClaw sans publication live.

Sortie attendue:

- agent cree mais non autonomise;
- repo initial commit possible.

### Cycle 3 - Configuration OpenClaw

Responsable: ClawdIA

Actions:

- ajouter agent a `openclaw.json`;
- definir allowlist de skills;
- definir model;
- definir permissions;
- verifier que l'agent apparait dans `openclaw sessions list` ou equivalent;
- relancer gateway si necessaire.

Sortie attendue:

- agent appelable manuellement.

### Cycle 4 - Run manuel 1

Responsable: morning-editorial-operator

Actions:

- produire un run complet sans publication;
- creer dossier `runs/YYYY-MM-DD-manual-1/`;
- produire rapport.

Sortie attendue:

- package complet ou blocage explique.

### Cycle 5 - Audit

Responsable: ClawdIA

Actions:

- auditer artefacts;
- noter manques;
- corriger instructions;
- renforcer checklist.

Sortie attendue:

- version instructions v0.2.

### Cycle 6 - Runs manuels 2 et 3

Responsable: morning-editorial-operator

Actions:

- refaire deux runs manuels;
- verifier amelioration;
- confirmer stabilite.

Sortie attendue:

- 3 runs manuels propres.

### Cycle 7 - Cron dry-run

Responsable: ClawdIA

Actions:

- creer cron 6h45;
- mode dry-run strict;
- rapport quotidien;
- surveillance 5 jours.

Sortie attendue:

- 5 runs consecutifs propres.

### Cycle 8 - Blog semi-auto

Responsable: morning-editorial-operator

Actions:

- autoriser publication blog si QA OK;
- interdire LinkedIn live;
- verifier live URL + presence `/blog`;
- rapporter.

Sortie attendue:

- 3 publications blog propres.

### Cycle 9 - Production stabilisee

Responsable: ClawdIA + morning-editorial-operator

Actions:

- documenter les incidents;
- consolider runbook;
- ajuster cron;
- decider du statut LinkedIn;
- archiver v1.0.

Sortie attendue:

- agent declare production-ready.

## 9. Criteres de blocage

L'agent doit s'arreter et rapporter si:

- aucun sujet ne depasse le seuil qualite;
- source SEO indisponible sans alternative fiable;
- article incomplet;
- image manquante en mode publication;
- build blog en echec;
- index blog non modifie;
- verification live impossible;
- ambiguite sur publication LinkedIn;
- conflit Git non resolu;
- credentials ou acces manquant;
- instruction contradictoire.

## 10. Rapport quotidien attendu

Format court:

```markdown
# Rapport editorial quotidien - YYYY-MM-DD

Statut: dry-run / package pret / publie / bloque
Sujet choisi:
Pourquoi ce sujet:
Article:
Visuel/BD:
LinkedIn:
QA:
Fichiers:
Blocages:
Decision attendue:
```

## 11. Questions ouvertes avant setup effectif

Ces points ne bloquent pas la specification, mais doivent etre tranches pendant le scaffold:

1. Le repo pipeline doit-il etre un repo GitHub distinct ou seulement un repo local versionne?
2. L'agent doit-il avoir son propre canal Telegram ou uniquement fonctionner via cron + rapports a Laurent?
3. Quels seuils chiffrés retenir pour le scoring sujet?
4. LinkedIn doit-il rester en brouillon permanent ou viser publication automatique apres stabilisation?
5. Le visuel quotidien doit-il etre obligatoire tous les jours ou seulement pour certains formats?

## 12. Prochaine action recommandee

Passer au Cycle 2: scaffold de l'agent et du repo.

Ordre d'execution:

1. creer `~/.openclaw/workspace-morning-editorial-operator`;
2. creer `~/.openclaw/agents/morning-editorial-operator/agent`;
3. creer `projects/morning-editorial-pipeline`;
4. generer `AGENTS.md`, `SOUL.md`, `USER.md`, `TOOLS.md`, `HEARTBEAT.md`;
5. generer `README.md`, `RUNBOOK.md`, `QUALITY.md`, templates et prompts;
6. proposer le bloc config OpenClaw;
7. ne pas activer de cron live avant un premier run manuel.
