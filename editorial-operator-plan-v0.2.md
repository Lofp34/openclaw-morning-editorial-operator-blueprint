# Editorial Operator - Plan d'action révisé v0.2

Date: 2026-07-14
Statut: à valider avant exécution
Objet: construire l'agent `editorial-operator` en une séquence contrôlée, sans publication live pendant la construction.

## 1. Décisions validées par Laurent

- Identité technique: `editorial-operator`.
- Nom lisible: `Editorial Operator`.
- Supprimer toutes les références au précédent nom, y compris noms de repo, fichiers, chemins, modes et textes.
- Modèle principal: `zai/glm-5.2`.
- ClawdIA pilote le cycle 2 et la création, pas Dave.
- Pipeline editorial dans un dépôt Git local versionné au départ.
- Canal Telegram dédié, limité à Laurent, token fourni ultérieurement.
- MATON est la couche de publication LinkedIn.
- Le visuel est obligatoire chaque jour: image hero du blog et carrousel BD complet.
- LinkedIn reste en dry-run pendant la stabilisation, puis passe en automatique après validation des seuils.

## 2. Résultat de l'audit du blueprint v0.1

Le dépôt GitHub de référence contient exactement quatre fichiers:

- `.nojekyll`
- `README.md`
- `index.html`
- le fichier Markdown du blueprint initial

L'arborescence annoncée dans le blueprint n'existe pas. Les huit prompts, cinq templates, scripts, documents, fichiers système et schémas restent donc à rédiger.

Les éléments listés dans le blueprint sont une spécification, pas des livrables déjà disponibles.

### Provenance prévue des huit prompts

- `01_research.md`: `serp-analyzer`, Google Search Console, Google Analytics et recherche web.
- `02_topic_selection.md`: règles de sélection du `blog-article-creator` et nouvelle grille de score.
- `03_brief.md`: références de brief et de calibration de la voix de Laurent.
- `04_article.md`: `blog-article-creator`, guide de voix, style français et boucle de révision.
- `05_visuals_bd.md`: `blog-hero-image-creator`, `bd-carousel` et `gpt-image-2-workshop`.
- `06_quality_gate.md`: contrôles finaux éditorial, SEO, factuel, visuel et technique.
- `07_publish_package.md`: protocoles de publication blog, MATON et LinkedIn.
- `08_report.md`: nouveau contrat de rapport, manifestes et reprise sur checkpoint.

Les cinq templates seront nouveaux. Ils seront reliés à des JSON Schemas afin que les sorties importantes ne reposent pas sur du Markdown libre.

## 3. Architecture cible

### Agent OpenClaw

- Agent ID: `editorial-operator`
- Workspace: `~/.openclaw/workspace-editorial-operator`
- Agent directory: `~/.openclaw/agents/editorial-operator/agent`
- Pipeline local: `~/.openclaw/workspace-editorial-operator/projects/editorial-pipeline`
- Clone site dédié: `~/.openclaw/workspace-editorial-operator/projects/site-LSD-repo`

Le clone site sera propre et indépendant. Le worktree actuel du copywriter contient des modifications; il ne sera ni réutilisé ni nettoyé par cette mission.

### Modèles

- Texte et orchestration: `zai/glm-5.2`, thinking `high`.
- Contrôle visuel explicite: `zai/glm-5v-turbo`.
- Génération hero et BD: appel explicite à `openai/gpt-image-2`.
- Aucun fallback image silencieux: un échec visuel bloque le package.
- Un fallback texte éventuel peut produire un brouillon dégradé, mais ne peut pas publier automatiquement.

### Fichiers système

Les noms canoniques sont `AGENTS.md`, `SOUL.md`, `USER.md`, `TOOLS.md`, `HEARTBEAT.md`, plus `IDENTITY.md`, `MEMORY.md` et `memory/`. Il n'existe pas de fichier canonique `SUB.md`.

- `AGENTS.md`: objectif, contexte, contraintes, commandes, contrôles et définition de terminé. Court et opérationnel.
- `SOUL.md`: identité Editorial Operator, ton, responsabilités et limites.
- `USER.md`: profil utile de Laurent et préférences éditoriales.
- `TOOLS.md`: particularités locales, accès, chemins et procédures. Pas de secrets.
- `HEARTBEAT.md`: contrôles légers seulement; aucune orchestration lourde.
- `IDENTITY.md`: nom, rôle et présentation.
- `MEMORY.md`: décisions durables; journal quotidien séparé.

Cette répartition suit les recommandations OpenAI Codex pour `AGENTS.md` et les spécifications OpenClaw des workspaces d'agents.

## 4. Skills cibles

### Réutilisés ou dérivés

- `blog-article-creator`
- `serp-analyzer`
- `seo`
- `google-search-console`
- `google-analytics`
- `generative-engine-optimization` si son apport est confirmé par test
- `blog-hero-image-creator`
- `bd-carousel`
- `self-improvement`

Les deux chemins copywriter fournis par Laurent résolvent vers le même dossier physique. Il n'y a donc qu'une source à auditer.

### Adaptations obligatoires via Skill Workshop

- Dériver un skill cible sans vocabulaire lié à l'ancien nom.
- Renommer le mode historique en `operator-autopilot`.
- Réconcilier les contradictions de formats, nombres de slides et tailles d'image.
- Utiliser l'asset de référence Laurent contenu dans le skill, sans chemin absolu externe.
- Créer `linkedin-publisher-maton` comme unique chemin de publication LinkedIn.

Le skill générique `publication-workflow` sert de source documentaire, mais ne sera pas activé comme orchestrateur concurrent. Les skills génériques LinkedIn/MATON qui se recouvrent ne seront pas tous exposés à l'agent.

## 5. Contrat éditorial quotidien

### Sélection du sujet sur 100 points

- Pertinence business et offres: 20
- Douleur et utilité pour la cible: 15
- Expertise et preuves disponibles: 15
- Opportunité SEO GSC/SERP: 15
- Originalité et absence de doublon: 10
- Actualité et momentum: 10
- Potentiel LinkedIn et visuel: 10
- Faisabilité du jour: 5

Règles:

- Score inférieur à 60: no-go et rapport.
- Score de 60 à 69: shortlist ou brouillon, pas de package complet.
- Score de 70 à 79: package complet possible si tous les garde-fous passent.
- Score de 80 ou plus: eligible à l'automatisation après stabilisation.
- Les contrôles anti-doublon, pertinence business, qualité des preuves et diversité des sources restent bloquants quel que soit le score.

### Entrées prioritaires

- Un seul fichier `*.publish-ok.md`: il est publié sans nouvelle réécriture éditoriale.
- Plusieurs fichiers `*.publish-ok.md`: blocage et alerte d'ambiguïté.
- Aucun fichier valide: recherche, score, brief et production autonomes.

### Visuels obligatoires

- Une hero blog paysage générée avec `openai/gpt-image-2`, optimisée WebP, avec texte alternatif.
- Un carrousel BD complet, séquentiel et cohérent, de 8 à 16 slides selon l'histoire.
- Export LinkedIn 1080 x 1350 par slide et PDF final.
- Export WebP pour l'article de blog.
- Contrôle des dimensions, lisibilité, continuité des personnages, contact sheet et inspection visuelle de chaque slide.
- Toute absence ou erreur visuelle bloque la publication.

## 6. Pipeline versionné

Le repo local contiendra au minimum:

- `README.md`, `RUNBOOK.md`, `QUALITY.md`, `CONFIG.md`, `CHANGELOG.md`
- `prompts/` avec les huit prompts rédigés
- `templates/` avec les cinq templates rédigés
- `schemas/` avec les schémas JSON de score, brief, état, QA et manifeste
- `scripts/preflight`, `run_daily`, `resume_run`, `validate_package` et publication dry-run/live
- `tests/fixtures/` pour les trois branches d'entrée
- `runs/<run-id>/STATE.json` et un manifeste immuable par exécution
- `docs/` pour politiques publication, rollback, LinkedIn, blog et incidents

Chaque étape écrit atomiquement son checkpoint. Une reprise ne refait pas une étape déjà validée.

## 7. MATON et sécurité LinkedIn

- Utiliser `gateway.maton.ai` et les connexions déjà testées pour LinkedIn, GSC et GA.
- Valider la version API LinkedIn au moment de l'implémentation au lieu de conserver une version figée.
- Ajouter `--dry-run`, idempotency key, registre des publications et détection des doublons.
- Vérifier visuellement le post après HTTP 201; le succès API ne prouve pas l'intégrité du rendu.
- Normaliser le texte LinkedIn pour éviter les troncatures connues.

Alerte: un script local de publication différée contient actuellement une clé d'API en dur. Avant toute réutilisation, la clé devra être révoquée ou tournée, le script assaini et le secret placé dans l'environnement Gateway. Aucune valeur de secret ne sera copiée dans le nouveau repo.

## 8. Telegram dédié

- Compte Telegram nommé `editorial-operator`.
- Token dans un fichier secret régulier, permissions `600`, jamais dans Git ni dans les prompts.
- Binding exact du compte vers l'agent `editorial-operator`.
- `dmPolicy: allowlist` avec l'ID Telegram de Laurent uniquement.
- Groupes désactivés par défaut.
- `defaultAccount` Telegram explicite pour ne pas modifier le routage des autres bots.
- Validation par `getMe`, redémarrage Gateway, test de binding et test de réponse privée.

## 9. Phases d'exécution one shot

1. Sauvegarde config, inventaire et rotation de la clé d'API exposée.
2. Renommage du blueprint en `Editorial Operator`, y compris repo, page, fichiers, chemins et modes.
3. Création du workspace, du repo pipeline Git local et du clone site propre.
4. Génération et validation des skills cibles via Skill Workshop.
5. Rédaction des fichiers système, prompts, templates, schémas, scripts et documentation.
6. Enregistrement de l'agent GLM 5.2 avec politique d'outils minimale.
7. Configuration Telegram après réception du token.
8. Tests unitaires, fixtures, contrôles de secrets et validation OpenClaw.
9. Un dry-run réel de bout en bout avec article, hero et carrousel réel, sans publication.
10. Audit final, corrections, second dry-run et rapport de preuve.
11. Activation du cron éditorial quotidien en mode dry-run.

La construction peut être réalisée en one shot. La stabilisation éditoriale et l'activation des publications live restent volontairement séparées car elles exigent plusieurs exécutions réelles.

## 10. Stabilisation et activation

- Palier 1: cinq runs planifiés consécutifs sans erreur, publication désactivée.
- Palier 2: trois publications blog propres et vérifiées avant automatisation blog complète.
- Palier 3: sept packages LinkedIn valides, sans doublon ni erreur de rendu, avant activation MATON live.
- Toute publication LinkedIn live reste désactivée jusqu'au GO d'activation après revue des preuves.
- Un échec remet le compteur du palier concerné à zéro et produit un rapport d'incident.

## 11. `/goal` et watchdog temporaire

Le `/goal` conserve l'objectif dans la session mais n'est pas, selon la documentation OpenClaw, une tâche de fond. Il sera donc complété par un cron temporaire ClawdIA toutes les cinq minutes.

Le watchdog:

- lit `STATE.json`, l'objectif et l'activité de la session exacte;
- ne fait rien si un run est actif;
- relance depuis le dernier checkpoint si le travail est incomplet et inactif depuis plus de huit minutes;
- utilise lock, run ID et idempotence pour interdire deux reprises concurrentes;
- alerte Laurent sur un vrai blocage au lieu de boucler sans fin;
- se désactive et se supprime dès que le rapport final est validé.

Limite explicite: un cron OpenClaw ne peut pas agir pendant une panne totale du Gateway. Le planning persiste et reprend après redémarrage, mais il ne remplace pas un superviseur système externe.

## 12. Critères de fin de la construction

- Plus aucune référence à l'ancien nom dans les artefacts cibles.
- Agent, workspace, skills et Telegram valides par OpenClaw.
- Aucun secret dans Git, les logs de preuve ou les fichiers de travail.
- Les trois branches d'entrée sont testées.
- Un run réel produit article, hero, BD, exports blog/LinkedIn, QA et rapport.
- Aucun effet de bord public pendant les tests.
- Repo pipeline propre, commité localement et reproductible.
- Watchdog supprimé après succès.

## 13. Seul prérequis restant avant l'exécution

Laurent doit fournir le token du nouveau bot Telegram par le canal secret prévu. Le token ne doit pas être collé dans un fichier versionné.
