# Revue critique du PRD v1.0 et amendements proposés

Statut : **proposition, à valider**. Le PRD v1.0 reste la source de vérité tant qu'un amendement n'est pas accepté. Chaque amendement accepté sera reporté dans `PRD.md` (v1.1) et consigné dans `Docs/DECISIONS.md`.

Les chiffres ci-dessous sont calculés à partir des valeurs du PRD, pas mesurés en jeu. Ils servent à repérer les incohérences avant de coder, pas à remplacer le playtest.

## 1. Ce qui est solide et qu'on garde

- La promesse : les missions montrées en publicité sont les missions jouables.
- Le contrôle à un doigt avec tir automatique.
- L'absence d'énergie, de gacha, de perte punitive et de difficulté cachée.
- La séparation règles C# pures / présentation Unity, et la simulation à pas fixe.
- La règle « ne jamais déclarer un test passé sans exécution ».
- Les gates P0 → P1 → P2 fondés sur des preuves.

## 2. Problèmes trouvés dans les règles

Classés par gravité. « Bloquant » signifie que le prototype ne pourra pas répondre à la question qu'il est censé trancher.

### 2.1 Les portes écrasent les builds (bloquant)

Le multiplicateur de DPS du groupe est linéaire : `1 + 0,12 × (N − 1)`.

| Effectif N | Multiplicateur de DPS |
|---:|---:|
| 1 | ×1,00 |
| 16 | ×2,80 |
| 48 | ×6,64 |
| 200 | ×24,88 |

En face, un build complet (damage niveau 3 et fire_rate niveau 3) donne au mieux ×2,8. Les portes pèsent donc jusqu'à neuf fois plus que les compétences. Le risque principal du §15 (« portes dominantes ou builds dominants ») est tranché par la formule avant tout playtest.

### 2.2 Le boss meurt avant sa première attaque (bloquant)

Boss Alpha a 600 HP et attaque toutes les 8 s. Au fusil, il tombe en 10,7 s avec 16 unités et en 4,5 s avec 48 unités. La télégraphie, l'esquive et la phase 2 ne seront presque jamais vues, alors que P0-05 exige de les valider.

### 2.3 Les armes courtes sont inutilisables (bloquant)

Le groupe avance à 5 u/s et un walker approche à 2 u/s : la distance se ferme à 7 u/s. La fenêtre de tir avant contact vaut portée ÷ 7.

| Arme | Fenêtre sur walker | Fenêtre sur runner | Ce que l'arme fait dans ce temps (N = 1) |
|---|---:|---:|---|
| drone | 2,6 s | 1,8 s | 41 dégâts |
| rifle | 2,1 s | 1,5 s | 43 dégâts |
| pistol | 1,7 s | 1,2 s | 20 dégâts, un walker tout juste |
| shotgun | 1,0 s | 0,7 s | un tir de 24 réparti sur 3, soit 8 par cible |
| flamethrower | 0,9 s | 0,6 s | environ 15 dégâts répartis sur 3 |

Le shotgun et le lance-flammes ne tuent rien avant le contact. Le PRD ne dit pas non plus si le groupe s'arrête ou ralentit au contact.

### 2.4 Le surplus de dégâts est perdu (important)

Le tir vise une seule cible. Dès qu'un tir tue en un coup, ajouter des unités n'accélère plus rien : le pistolet plafonne à 2 éliminations par seconde et le fusil à 5, quel que soit N. Le plafond de 200 unités n'a alors aucun effet contre les ennemis de base.

### 2.5 La compétence fire_rate ne fait rien telle qu'écrite (important)

Le PRD pose `dégâts par tir = DPS ÷ cadence`. Si la cadence monte et que le DPS reste fixe, les dégâts par tir baissent d'autant. Il faut préciser que les dégâts par tir sont figés à partir des valeurs de base.

### 2.6 Le système de builds est hors d'atteinte au P0 (important)

- M01 rapporte 20 XP : un seul choix (seuil 10), le seuil 25 n'est jamais atteint.
- La compétence chain vient du scientifique, sauvé à M03, crédité à la victoire. Chain et la fusion Tesla n'existent donc qu'en rejouant, après la fin du contenu.
- Les seuils 10/25/45 sont ambigus (cumulés ou successifs).

### 2.7 Le camp n'offre aucune décision au P0 (important)

- Tout le camp P0 coûte 190 scrap et 45 food. Le joueur reçoit 360 scrap et 115 food hors ramassage. Tout est achetable sans choisir.
- La nourriture n'a aucune dépense après les trois achats.
- La capacité de départ est 10 et la population maximale du P0 est 10 : le Shelter ne change rien de visible.
- La Farm produit une ressource sans usage.

Le pilier « reconstruction visible » est donc celui que le prototype teste le moins.

### 2.8 Incohérences mineures

| Sujet | Problème | Correction proposée |
|---|---|---|
| Durée de M01 | 300 u à 5 u/s font 60 s, hors de la cible 90–150 s | Cible tutoriel 60–90 s, missions standard 90–150 s |
| Médecin | « +20 HP au départ » alors que le héros part à 100/100 | Un soin automatique de 20 HP sous 30 % de vie, une fois par mission |
| Armes P0 | Seul le déblocage du fusil est défini | Voir amendement A7 |
| Cône de 65° | Angle total ou demi-angle non précisé | Angle total, soit ±32,5° |
| Portes | Règle à x = 0 non précisée | Deux portes couvrent la route ; x < 0 à gauche, x ≥ 0 à droite |
| Sauvetage | 0,5 s à 5 u/s imposent une zone d'au moins 2,5 u | Zone de 4 u de long |
| Compétence heal | « 3 niveaux » n'a pas de sens pour un soin immédiat | Consommable, proposé au plus 3 fois |
| `appliedTransactions` | Liste qui grossit sans fin | Garder les 64 derniers identifiants |
| Déterminisme | Les flottants varient selon l'appareil | Garantie limitée à « même build, même plateforme, même seed, mêmes entrées » |
| 40 modèles pour 200 unités | Au-delà de 40, une porte ne change plus rien à l'écran | Plafond logique P0 à 60, configurable |
| Mesure D30 | À 1 000 joueurs, 5 % ± 1,35 point, soit ± 27 % en relatif | Ne pas décider sur D30 avant 3 000 joueurs ou plusieurs cohortes |

## 3. Amendements proposés

Toutes les valeurs sont des points de départ configurables.

### A1 — Deux variantes de puissance du groupe, à tester l'une contre l'autre

- Variante A, portes dominantes : formule actuelle, `1 + 0,12 × (N − 1)`.
- Variante B, builds dominants : `1 + 0,35 × √(N − 1)`.

| N | Variante A | Variante B |
|---:|---:|---:|
| 2 | ×1,12 | ×1,35 |
| 6 | ×1,60 | ×1,78 |
| 16 | ×2,80 | ×2,36 |
| 48 | ×6,64 | ×3,40 |
| 200 | ×24,88 | ×5,94 |

Dans les deux cas, chaque renfort garde ses 10 HP. En variante B, une porte reste précieuse pour survivre mais la puissance de feu vient surtout du build. La variante est un champ de configuration et figure dans chaque événement analytics.

### A2 — Report du surplus de dégâts

Quand un tir tue sa cible, le surplus passe à la cible suivante la plus proche dans le cône, jusqu'à épuisement. Les armes multi-cibles gardent leur intérêt : elles frappent plusieurs ennemis même sans surplus.

### A3 — Armes de zone à dégâts pleins et règle de contact

- Shotgun : 24 dégâts à chacune des 3 cibles, non répartis.
- Lance-flammes : 18 DPS à chacune des 3 cibles.
- Quand un ennemi est au contact du groupe, l'avance tombe à 2 u/s. Le défilement s'arrête dans l'arène du boss.

### A4 — Cadence et cumul des bonus

`dégâts par tir = DPS de base ÷ cadence de base`, valeur figée. Les niveaux d'une même compétence s'additionnent ; les compétences différentes se multiplient entre elles.

### A5 — Boss dimensionné par la durée du combat

Les HP du boss sont dérivés d'une durée cible de 30 à 40 s pour un joueur de référence, soit 3 à 4 attaques. Valeur initiale : 3 200 HP en variante B, 7 500 HP en variante A, à recalculer par le simulateur.

- Première attaque 3 s après l'entrée dans l'arène.
- Trois motifs lisibles, chacun esquivable par glissement : moitié gauche, moitié droite, bande centrale.
- Phase 2 inchangée.

### A6 — Builds accessibles dès la première partie

- Quatre choix par mission, seuils cumulés 8, 20, 36 et 56 XP.
- M01 passe à 26 walkers pour offrir deux choix.
- Le scientifique est sauvé à M02 et l'ingénieur à M03 ; chain devient disponible dès M03 et la fusion Tesla peut apparaître contre le boss.
- Le fusil est alors débloqué à la victoire de M02, voir A7.

### A7 — Déblocage des cinq armes du P0

| Arme | Déblocage |
|---|---|
| pistol | départ |
| shotgun | première victoire M01 |
| rifle | première victoire M02 |
| flamethrower | Workshop L1 (ingénieur, après M03) |
| drone | 6 étoiles sur 9 (voir A9) |

L'écran de choix d'arme est sauté tant qu'une seule arme est disponible.

### A8 — Un camp qui oblige à choisir

- P0 en une seule ressource, le scrap. La nourriture et la Farm reviennent au MVP, quand elles ont une dépense. Le champ `food` reste dans la sauvegarde.
- Bâtiments P0 : Shelter, Workshop, Barracks, niveaux 1 et 2.
- Chaque bâtiment a un effet visible en mission : Barracks ajoute 2 unités au départ par niveau, Workshop débloque une arme puis donne +10 % de DPS, Shelter L2 ajoute un soin de 15 HP à mi-mission.
- Coût total 616 scrap pour environ 470 gagnés au premier passage : le joueur doit choisir, et rejouer a un but.
- Le modèle 3D de chaque bâtiment change à chaque niveau, et le camp est montré avant et après chaque mission.

### A9 — Trois objectifs facultatifs par mission

Une étoile par objectif : terminer la mission, sauver le spécialiste, finir avec au moins un nombre donné d'unités. Aucune récompense aléatoire, aucun blocage de progression. Les étoiles donnent une raison de rejouer avec un autre build et débloquent le drone.

### A10 — Un critère chiffré pour le gate P0

« Amusant » est remplacé par des seuils, sur 10 à 20 testeurs en session libre de 15 minutes :

| Mesure | Seuil proposé |
|---|---|
| Testeurs qui finissent M01 sans aide | au moins 80 % |
| Testeurs qui relancent une mission d'eux-mêmes après M03 | au moins 60 % |
| Note médiane « j'ai envie de rejouer » sur 5 | au moins 4 |
| Testeurs capables de citer un choix de build ou de camp qu'ils ont fait | au moins 70 % |
| Plantages bloquants | 0 |
| Images par seconde sur l'appareil de référence | au moins 30 |

Avec 20 testeurs, ces pourcentages sont indicatifs ; ils servent de garde-fou, pas de preuve statistique.

## 4. Ce qu'on retire ou reporte du P0

| Élément | Décision proposée | Raison |
|---|---|---|
| Tests de migration de sauvegarde | Reportés au premier changement de schéma | Il n'existe qu'une version |
| Taille de texte réglable, trois niveaux de qualité | MVP | Sans effet sur la question « est-ce amusant » |
| Nourriture, Farm | MVP | Aucune dépense au P0 |
| Localisation EN complète | Tables de chaînes dès le départ, relecture EN au MVP | Coût quasi nul maintenant, coûteux à rattraper |
| Plafond logique de 200 unités | 60 au P0 | Lisibilité des portes |

## 5. Décisions qui vous reviennent

1. Accepter, refuser ou modifier chacun des amendements A1 à A10.
2. Version exacte d'Unity et appareil Android de référence.
3. Licence Unity pour l'intégration continue, ou CI limitée aux tests .NET du cœur.
4. Nom définitif : « Zero to Civilization » n'est pas vérifié juridiquement.
