# Consignes de génération des cartes — ScroPed (pédiatrie)

Ce document décrit comment produire les fichiers de cartes lus par l'application (dossier `Recos/`). Il s'adresse à toute IA ou personne qui génère un lot. L'app est une version pédiatrie de ScrollMed : des micro-cartes à faire défiler, destinées à la formation d'un interne de pédiatrie (pédiatrie générale, urgences, néonatologie).

> ⚠️ Les cartes sont un outil de **révision**, pas une prescription. Chaque lot doit être relu par un professionnel avant usage clinique, et les chiffres vérifiés dans le RCP, le Vidal ou la recommandation source.

---

## 1. Fonctionnement technique

- L'app lit **tous les fichiers `.json`** du dossier `Recos/` (synchronisation GitHub). Les autres fichiers (dont ce `.md`) sont ignorés.
- Un fichier = un tableau JSON de cartes. Nom : `lotN.json` (N = numéro suivant, jamais réutilisé).
- Une carte est identifiée par `module` + `hook` : **deux cartes ne doivent jamais avoir le même couple** (la synchro les considérerait comme identiques, et la progression/favoris y sont rattachés).
- Le `module` définit le thème : couleur, avatar et activation/désactivation dans le menu ☰. Le nom doit être **identique à l'octet près** d'une carte à l'autre pour regrouper des cartes dans un même module.
- Le JSON doit être valide : pas de commentaire, pas de virgule finale, guillemets droits `"` (échapper `\"` à l'intérieur d'un texte), UTF-8.

## 2. Structure d'une carte

| Champ | Obligatoire | Rôle |
|---|---|---|
| `module` | oui | Thème de la carte (ex : « Bronchiolite »). |
| `hook` | oui | Accroche, 8 mots maximum (recto d'un quiz). |
| `body` | oui | Message clé (1 à 3 phrases) ; verso d'un quiz. |
| `detail` | non (recommandé) | Nuance, contexte, contre-indication, piège. Affiché sous « … plus ». |
| `source` | oui | Organisme + année, ou référence précise. |
| `grade` | non | Niveau de preuve **seulement s'il est connu** ; sinon omettre le champ. |
| `tags` | oui | 2 à 4 mots-clés, en minuscules (ou nom de classe pour une posologie). |
| `quiz` | quiz uniquement | `true` : carte recto/verso, la réponse est masquée. |
| `format` | quiz uniquement | `posologie`, `reperes`, `cas` ou `quiz` (voir §3). |

## 3. Les types de cartes

### 3.1 Carte « recommandation » (type standard)

Un **seul message clé actionnable** issu d'une vraie recommandation.

- `hook` : affirmation courte et percutante (« Fièvre avant 3 mois : toujours l'hôpital »).
- `body` : la règle, avec seuil / dose / délai si la source le donne.
- `detail` : exception, mécanisme, pièges.
- Pas de champ `quiz`. Exemples : `Recos/lot1.json` (pédiatrie générale), `Recos/lot2.json` (néonatologie).

### 3.2 Carte quiz « posologie » (`"quiz": true, "format": "posologie"`)

Flashcard façon Anki : l'interne doit se rappeler la dose avant de voir la réponse.

- `module` : `"Posologies pédiatriques"`.
- `hook` : **nom du médicament uniquement**, + contexte en quelques mots si ambigu (« Ceftriaxone (infection bactérienne sévère) »).
- `body` : **une** posologie : dose en **mg/kg** (ou µg/kg), par dose **et** par jour si pertinent, voie, fréquence, dose maximale.
- `detail` : adaptation selon l'âge/terme, contre-indications majeures, surveillance, formes galéniques.
- `tags` : classe thérapeutique + contexte. Exemple : `Recos/lot3.json`.

### 3.3 Carte quiz « repères » (`"quiz": true, "format": "reperes"`)

Valeurs normales, formules, scores, jalons de développement, volumes.

- `module` : `"Repères pédiatriques"`.
- `hook` : la question sous forme de titre (« Poids estimé (1 à 10 ans) »).
- `body` : la réponse chiffrée ou la liste des items.
- `detail` : tableau complet, limites d'utilisation, signes d'alerte. Exemple : `Recos/lot4.json`.

### 3.4 Carte quiz « cas clinique » (`"quiz": true, "format": "cas"`)

Mini-vignette clinique, 1 à 2 phrases (âge, motif, 2-3 éléments clés), qui se termine par une question.

- `module` : `"Cas cliniques"`.
- `hook` : la vignette + la question (jusqu'à ~200 caractères ; la police se réduit automatiquement au-delà de 70).
- `body` : diagnostic + conduite à tenir **dans l'ordre** des priorités.
- `detail` : pièges, signes de gravité, suite de la prise en charge. Exemple : `Recos/lot5.json`.

### 3.5 Carte « culture / histoire » (type standard)

Anecdote historique, droit, éthique, figure de la pédiatrie. Même structure que 3.1, `module` : `"Histoire et culture pédiatrique"`, pas de `grade`. Les faits (dates, noms) doivent être vérifiables. Exemple : `Recos/lot6.json`.

## 4. Thèmes à couvrir

Varier les modules d'un lot (pas plus de 2-3 cartes par module dans un lot de 25).

- **Pédiatrie générale / urgences** : bronchiolite, asthme, fièvre, infections ORL et respiratoires, gastro-entérite et déshydratation, convulsions, anaphylaxie, purpura fébrile, méningite, infection urinaire, diabète de l'enfant, drépanocytose, traumatologie, intoxications, laryngite, appendicite, invagination.
- **Néonatologie** : réanimation en salle de naissance, adaptation à la vie extra-utérine, détresse respiratoire, ictère, hypoglycémie, infection néonatale, prématurité et nutrition, dépistages, allaitement, douleur.
- **Pharmacologie** : posologies, spécialités fréquentes, interactions, contre-indications selon l'âge.
- **Développement et suivi** : croissance, développement psychomoteur, vaccinations, alimentation, sommeil, carnet de santé.
- **Protection de l'enfance et éthique** : maltraitance, information préoccupante, droit de l'enfant, annonce, consentement.
- **Cas cliniques**, **repères chiffrés**, **histoire**.

## 5. Sources autorisées

Utiliser des références **réelles et vérifiables** : HAS, SFP, GPIP, SPILF, ANSM, CNGOF, Collège de pédiatrie, ERC/ILCOR, AAP, NICE, ESPGHAN, ESPID, EAACI, GINA, OMS, Surviving Sepsis Campaign (enfants), RCP des médicaments.

- Citer l'organisme + l'année (+ le titre court). **Ne jamais inventer une année, un titre ou un grade.** En cas de doute, citer l'organisme et le thème sans année.
- Les recommandations françaises évoluent (calendrier vaccinal, bronchiolite, VRS…) : mentionner « édition en vigueur » quand l'information est susceptible de changer.

## 6. Règles de qualité

1. **Un message par carte.** Ne pas mélanger deux recommandations, deux médicaments ou deux tranches d'âge dans le même `body`.
2. **Précision pédiatrique obligatoire** : toujours préciser l'âge, le poids ou le terme concerné (« nouveau-né », « < 3 mois », « > 20 kg », « ≥ 3 ans »), les doses **par kilo**, l'**unité** (mg, µg, mL), la **voie**, la **fréquence** et la **dose maximale**.
3. **Ne jamais inventer un chiffre.** Si un seuil ou une dose n'est pas certain, formuler la carte de façon qualitative ou donner la fourchette admise ; signaler la variabilité dans `detail` (« selon le protocole local »).
4. **Pas de concentration d'erreur à risque** : vérifier les facteurs ×10 (µg/mg, 1/1000 vs 1/10 000), la dilution et l'unité (mL/kg vs mg/kg).
5. **Langage clinique sobre**, français, phrases courtes, abréviations usuelles (SA, SpO2, IV, IM, IVSE, per os).
6. **`hook` ≤ 8 mots**, sans point final, affirmatif. Pour un `cas`, la vignette fait exception.
7. **`body` = 1 à 3 phrases**, autoportant (compréhensible sans le `hook`).
8. **`detail`** apporte ce que le `body` ne dit pas (jamais une redite).
9. **Diversité** : au moins 8 modules différents pour 25 cartes ; mélanger standard, quiz et cas.
10. Aucun nom de patient, aucune donnée identifiante, aucune image.
11. **Niveau de difficulté** : les lots 1 à 6 sont de niveau « premier cycle de l'internat » (règles de base). À partir du lot 7, viser un niveau d'interne confirmé : diagnostics différentiels, pièges, ordre des priorités, seuils chiffrés précis, urgences à diagnostic non évident (acidocétose, torsion, volvulus, cardiopathie ducto-dépendante…), scores moins courants, doses d'urgence et d'endocrinologie/néonatologie. Éviter les messages déjà couverts par un lot précédent.

## 7. Procédure de génération d'un lot

1. Choisir le numéro de lot (`lotN.json`, N = dernier numéro + 1) et la thématique ou le format du lot.
2. Lister les sujets et vérifier qu'ils n'existent pas déjà : rechercher le `hook` et le `module` dans `Recos/*.json`.
3. Rédiger les cartes selon §2 et §3.
4. Valider le fichier (commande ci-dessous) : JSON valide, champs obligatoires, doublons `module + hook`, `hook` trop long.
5. Relecture clinique d'un professionnel (chiffres, âges, doses, sources).
6. Déposer le fichier dans `Recos/` puis synchroniser l'app (☰ → Importer → Synchronisation GitHub).

### Commande de validation

```bash
python3 - <<'PY'
import json, glob, collections
seen = collections.Counter()
for f in sorted(glob.glob('Recos/*.json')):
    cards = json.load(open(f, encoding='utf-8'))
    for c in cards:
        for k in ('module', 'hook', 'body', 'source', 'tags'):
            if not c.get(k): print(f, 'champ manquant', k, '->', c.get('hook'))
        if c.get('quiz') and c.get('format') not in ('posologie', 'reperes', 'cas', 'quiz'):
            print(f, 'format de quiz inconnu ->', c.get('hook'))
        if len(c['hook'].split()) > 8 and c.get('format') != 'cas':
            print(f, 'hook > 8 mots ->', c['hook'])
        seen[(c['module'].lower().strip(), c['hook'].lower().strip())] += 1
    print(f, len(cards), 'cartes')
print('doublons :', [k for k, v in seen.items() if v > 1])
PY
```

## 8. Modèles de prompt à donner à une IA

Les mêmes consignes sont disponibles dans l'app (☰ → Importer) sous forme de prompts prêts à copier : lot de 25 recommandations, lot de 20 posologies, lot de 20 repères / cas cliniques. Le fichier produit s'appelle `lot.json` ; le renommer en `lotN.json` avant de le déposer dans `Recos/`.

Exemple de squelette (carte standard) :

```json
{
  "module": "Bronchiolite",
  "hook": "Bronchiolite : pas de médicaments en routine",
  "body": "Chez le nourrisson de moins de 12 mois au 1er épisode de bronchiolite, ne pas prescrire de bronchodilatateurs, corticoïdes ni antibiotiques en routine.",
  "detail": "Les antibiotiques ne sont indiqués qu'en cas de surinfection documentée.",
  "source": "HAS 2019 — Bronchiolite aiguë du nourrisson",
  "tags": ["bronchiolite", "nourrisson"]
}
```

Exemple de squelette (quiz posologie) :

```json
{
  "module": "Posologies pédiatriques",
  "quiz": true,
  "format": "posologie",
  "hook": "Paracétamol (antalgique, antipyrétique)",
  "body": "15 mg/kg par dose toutes les 6 h (maximum 60 mg/kg/j), per os, IV ou rectal.",
  "detail": "Intervalle minimal de 4 h. Chez le nouveau-né, doses réduites selon l'âge gestationnel.",
  "source": "RCP ; ANSM",
  "tags": ["antalgique", "antipyrétique"]
}
```
