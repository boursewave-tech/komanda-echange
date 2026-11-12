# Plan de conception — ZERO TO CIVILIZATION

Ce plan s'appuie sur `PRD.md` v1.0 et sur les amendements A1 à A10 de `Docs/REVUE_PRD.md`. Tant que les amendements ne sont pas validés, les lots L0 à L2 restent valables tels quels : ils ne dépendent d'aucun amendement contesté, car toutes les valeurs sont en configuration.

## 1. Les trois piliers

Chaque fonctionnalité doit servir au moins un pilier, sinon elle sort du prototype.

1. **Une mission lisible en dix secondes.** Un doigt, tir automatique, choix de porte compréhensible sans texte.
2. **Un build qu'on peut raconter.** À la fin d'une mission, le joueur sait dire ce qu'il a choisi et pourquoi il a gagné ou perdu.
3. **Une reconstruction qu'on voit.** Chaque achat au camp change le décor du camp et le déroulement de la mission suivante.

## 2. La question que le prototype doit trancher

« Un joueur qui a fini les trois missions relance-t-il une partie de lui-même ? »

Tout le plan est ordonné pour obtenir cette réponse le plus tôt possible. Le risque principal du PRD est un combat répétitif ; il est donc testé au lot L2, avant la sauvegarde, l'interface finale et la build Android.

## 3. Architecture

```
Assets/Game/
  Core/            C# pur, aucune référence à UnityEngine
  Gameplay/        MonoBehaviours qui pilotent le Core
  Presentation/    HUD, vues du camp, écrans
  Infrastructure/  sauvegarde, analytics local, configuration, horloge
  Content/         ScriptableObjects : missions, armes, ennemis, bâtiments
  Art/  Audio/  Tests/
Tools/Simulator/   application console .NET qui rejoue des missions sans Unity
Docs/
```

Principes :

- **Le Core contient toute la règle du jeu.** Il avance par pas fixes de 1/30 s et ne connaît ni le temps réel ni Unity. Unity lui fournit les entrées et affiche son état, avec interpolation pour le rendu à 60 images par seconde.
- **Le Core est aussi un projet .NET classique.** Les mêmes sources sont compilées par Unity et par `dotnet test`. Les règles sont ainsi testables sur une machine sans Unity, y compris en intégration continue sans licence.
- **Tout chiffre d'équilibrage est une donnée.** Formule de groupe, armes, ennemis, seuils d'XP, coûts : rien n'est codé en dur.
- **Pas de physique Unity dans la règle.** Collisions par volumes simples calculés dans le Core.

### Le simulateur d'équilibrage

Un outil console lance des centaines de missions avec des joueurs automatiques : un qui prend toujours la meilleure porte, un qui choisit au hasard, un qui ne bouge pas. Il produit pour chaque mission le taux de victoire, la durée, l'effectif final, la durée du combat de boss et le scrap gagné.

Il sert à trois choses : vérifier qu'une mission est terminable avant de la faire jouer, comparer les variantes A et B, et détecter une régression d'équilibrage dans les tests automatiques.

## 4. Lots de réalisation

Les identifiants du backlog du PRD sont conservés ; seul l'ordre des gates change.

### L0 — Socle (P0-01)

| Livrable | Critère d'acceptation |
|---|---|
| Projet Unity, version verrouillée, URP, Android ARM64 | Le projet s'ouvre et compile sans erreur |
| Assemblies Core, Gameplay, Presentation, Infrastructure, Tests | Core compile sans référence à UnityEngine |
| Projet .NET du Core et de ses tests | `dotnet test` passe sur une machine sans Unity |
| `SeededRandom`, boucle à pas fixe, chargement de configuration | Même seed et mêmes entrées donnent le même résultat, test à l'appui |
| `Docs/STATUS.md`, `Docs/DECISIONS.md` | À jour à la fin du lot |

### L1 — Le cœur du combat en boîtes grises (P0-02, P0-03, P0-04)

| Livrable | Critère d'acceptation |
|---|---|
| Glissement et caméra | Position bornée à [−5, 5], vitesse latérale au plus 12 u/s, pause sur perte de focus |
| Tir automatique | Cible la plus proche dans le cône, départage par identifiant, DPS mesuré conforme à la configuration à 2 % près |
| Report du surplus (A2) et règle de contact (A3) | Tests unitaires sur les cas limites |
| Ennemis walker, runner, tank | Aucun n'apparaît sur le héros, pool sans fuite sur 10 minutes |
| Portes et groupe | 1 + 15 = 16, 16 × 3 = 48, pénalité bornée à 1, activation unique |
| Formules de groupe A et B (A1) | Sélection par configuration, tables de valeurs testées |
| Simulateur, première version | Rejoue une mission de test avec les trois joueurs automatiques |

### L2 — Une mission complète, puis premier verdict (P0-05, P0-06)

| Livrable | Critère d'acceptation |
|---|---|
| Machine d'états de mission | Transitions invalides rejetées, une seule résolution finale |
| Boss Alpha (A5) | Trois motifs annoncés 1,2 s avant, phase 2 à 50 %, combat de 30 à 40 s au simulateur |
| XP et choix de compétences (A4, A6) | Options distinctes, plafonds respectés, jeu en pause pendant le choix |
| Une mission de type M03 jouable de bout en bout | Victoire et défaite atteignables |

**Gate G0 — verdict interne.** Cinq personnes jouent la mission en variantes A et B sur l'appareil de référence. On continue si le combat tient dix parties sans lassitude déclarée et si une variante est préférée. Sinon, on retravaille le combat avant d'investir dans le reste.

### L3 — La boucle complète (P0-07, P0-08, P0-09)

| Livrable | Critère d'acceptation |
|---|---|
| Sauvetage | Zone de 4 u, crédité à la victoire seulement, jamais de doublon |
| Économie | Coût exact, fonds insuffisants refusés, transaction rejouée sans double effet |
| Camp (A8) | Trois bâtiments, deux niveaux, effet visible en mission, modèle qui change |
| Sauvegarde | Écriture atomique, copie de secours, fichier corrompu signalé sans écrasement |
| Récompenses | Première victoire, rejeu à 50 %, défaite à 25 % du ramassé, tests unitaires |

### L4 — Contenu et prise en main (P0-10, P0-11)

| Livrable | Critère d'acceptation |
|---|---|
| M01, M02, M03 | Chacune terminable par le joueur automatique « meilleure porte » et perdable par celui qui ne bouge pas |
| Cinq armes et leurs déblocages (A7) | Chaque arme gagne au moins une mission au simulateur |
| Objectifs facultatifs (A9) | Trois étoiles par mission, enregistrées |
| Tutoriel en trois consignes | Parcours complet sans blocage, première ouverture directement dans M01 |
| Interface | Zones sûres, cibles tactiles d'au moins 48 dp, textes FR et EN par tables |

### L5 — Mesure et build (P0-12)

| Livrable | Critère d'acceptation |
|---|---|
| Analytics local en JSONL | Export valide, variante d'équilibrage dans chaque événement |
| APK Android | Installé et lancé sur l'appareil de référence |
| Rapport de performance | Mesuré sur appareil : images par seconde, mémoire, test de charge 80 ennemis et 40 alliés pendant 10 minutes |

**Gate P0 — verdict externe.** Playtest de 10 à 20 personnes selon le protocole de la section 6.

## 5. Contenu cible du prototype

| Mission | Longueur | Durée visée | Ennemis | Spécialiste | Ce qu'elle enseigne |
|---|---:|---:|---|---|---|
| M01 | 300 u | 60–90 s | 26 walkers | médecin | glisser, choisir une porte, sauver |
| M02 | 450 u | 90–120 s | 35 walkers, 10 runners | scientifique | obstacle destructible, premier vrai build |
| M03 | 550 u | 130–150 s | 40 walkers, 15 runners, 4 tanks, Boss Alpha | ingénieur | boss, compétence chain, fusion Tesla |

Les effectifs d'ennemis seront ajustés par le simulateur une fois la variante de groupe choisie.

## 6. Protocole du playtest P0

- 10 à 20 personnes extérieures à l'équipe, moitié en variante A, moitié en variante B.
- Session libre de 15 minutes, sans explication orale, sur un téléphone fourni.
- Données relevées automatiquement : missions lancées, résultats, durées, portes prises, compétences choisies, achats au camp, rejeux.
- Trois questions à la fin : envie de rejouer sur 5, « qu'avez-vous choisi et pourquoi », « qu'est-ce qui vous a gêné ».
- Seuils de réussite : ceux de l'amendement A10.

## 7. Qualité

- Tests unitaires du Core écrits avec chaque règle : portes, DPS, réduction de dégâts, coûts, récompenses, transactions, spécialiste unique, aléatoire stable.
- Tests PlayMode : tutoriel complet, victoire, défaite, pause, mise en arrière-plan, interruption de mission.
- Un test du simulateur échoue si le taux de victoire d'une mission sort de sa fourchette.
- Aucun résultat n'est consigné dans `Docs/STATUS.md` sans avoir été exécuté.

## 8. Risques et parades

| Risque | Parade |
|---|---|
| Combat répétitif | Gate G0 dès le lot L2, variantes A et B, objectifs facultatifs |
| Équilibrage fait à l'aveugle | Simulateur dès le lot L1 |
| Camp perçu comme un menu | Effets visibles en mission, camp montré avant et après chaque mission |
| Performances sur entrée de gamme | Pooling dès L1, mesure sur appareil à chaque lot à partir de L2 |
| Dérive du périmètre | Liste de retraits de la revue, règle des trois piliers |
| Unity indisponible sur un poste ou en CI | Core testable par .NET seul |

## 9. Avant de démarrer L0

Trois réponses sont nécessaires :

1. Les amendements A1 à A10 : acceptés, refusés ou modifiés.
2. La version d'Unity et l'appareil Android de référence.
3. Le poste de développement : cet environnement cloud n'a ni Unity ni .NET installés. Le Core, ses tests et le simulateur peuvent y être écrits après installation de .NET ; tout ce qui touche à l'éditeur Unity devra être vérifié sur une machine équipée.
