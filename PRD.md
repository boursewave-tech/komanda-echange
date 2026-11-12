# ZERO TO CIVILIZATION — PRD v1.0

Statut : proposition de conception à valider par prototype. Nom de travail non vérifié juridiquement. Langue du document : français ; identifiants techniques en anglais.

## 1. Vision et objectifs
Jeu mobile 3D hybrid-casual, portrait, solo hors ligne : partir d'un survivant, combattre, recruter des spécialistes et reconstruire une civilisation visible. Promesse : les missions publicitaires sont les missions réellement jouables. Public cible : amateurs de shooters accessibles, roguelites et construction, 13+ sous réserve de classification. Aucun mécanisme de perte punitive liée à l'absence, aucune récompense aléatoire payante. Android d'abord, iOS ensuite. Le jeu doit rester agréable sans paiement.

Objectifs expérimentaux (non garantis) : première mission terminée par 80 % des nouveaux joueurs ; D1 ≥40 %, D7 ≥15 %, D30 ≥5 %. Ambition après optimisation : 45/18/8 %. Mesurer aussi durée de session, diversité des builds, abandon par mission, stabilité et coût d'acquisition. Ne pas confondre installations et utilisateurs actifs.

## 2. Arbitrages du comité simulé
- Game Director : privilégier la reconstruction visible plutôt qu'une accumulation abstraite de chiffres.
- Gameplay : garder le déplacement à un doigt et le tir automatique ; ajouter des choix de route et de build.
- Systems : séparer les renforts temporaires de la population permanente ; éviter l'inflation exponentielle.
- Technique : simulation locale déterministe, données configurables, pas de serveur au MVP.
- Art : 3D stylisée originale, lisibilité prioritaire sur photoréalisme.
- Rétention : missions courtes, variété, objectifs choisis, aucune obligation quotidienne.
- Monétisation : aucune publicité au prototype ; récompenses facultatives après validation.
- QA : chaque fonctionnalité doit avoir un test reproductible.

## 3. Périmètre
Prototype P0 : 3 missions, 5 armes, 3 spécialistes, 1 boss, 1 camp, sauvegarde locale, instrumentation locale. MVP P1 : 12 missions réparties sur 3 zones, 2 boss, 6 bâtiments, 8 armes, 6 compétences, 3 spécialistes, tutoriel, paramètres et localisation FR/EN. P2 : carte étendue, nouvelles régions, davantage de builds, cosmétiques et publicité facultative. P3 : coopération et services en ligne uniquement après validation économique et technique. Exclus du MVP : PvP, clans, chat, monde ouvert, gacha, multijoueur, construction libre, simulation individuelle de milliers d'habitants.

## 4. Boucles de jeu
Mission (90–150 s) : sélectionner mission → choisir arme → avancer automatiquement → viser automatiquement → esquiver et choisir une porte → sauver ou non un spécialiste → choisir des améliorations → boss/extraction → bilan → camp. Camp (30–90 s) : attribuer les ressources, construire, améliorer, sélectionner prochaine mission. Une défaite donne 25 % des matériaux ramassés, aucun recrutement permanent ; pas de perte de bâtiments. Une victoire donne les matériaux ramassés, la récompense fixe et les recrutements uniques. Aucun système d'énergie bloquant les tentatives.

## 5. Contrôles et simulation
Caméra perspective fixe derrière le groupe, route de largeur 12 unités, déplacement avant 5 u/s. Glissement horizontal relatif : 1 largeur d'écran = 12 unités, clamp x [-5,5], vitesse latérale max 12 u/s. Tir automatique vers ennemi vivant le plus proche dans un cône avant de 65° et à portée ; départage par identifiant stable. Pause pendant menus, choix d'amélioration et perte de focus. Simulation fixe 30 Hz, rendu cible 60 FPS, minimum 30 FPS. Aucun input requis pour tirer. Les collisions utilisent des volumes simples ; projectiles visuels découplés des dégâts hitscan. Formation compacte, renforts représentés par un maximum de 40 modèles ; compteur logique plafonné à 200. Pas de friendly fire.

### 5.1 Règles de combat initiales
Héros : 100 HP ; groupe initial 1 unité ; dégâts reçus réduisent d'abord les HP du groupe (10 HP par renfort), puis ceux du héros. Un renfort perdu ne revient pas automatiquement. DPS groupe = DPS arme × (1 + 0,12 × (N−1)), N plafonné à 200 ; coefficient à ajuster. Dégâts par tir = DPS / cadence. Critique désactivé au prototype. Contact ennemi : dégâts toutes les 0,5 s, pas à chaque frame. Héros à 0 HP = défaite. Boss vaincu + arrivée extraction = victoire ; mission sans boss = extraction seule. Les portes s'activent une seule fois au franchissement de leur plan, selon la position du héros ; aucune double activation. +n, ×n, −n ; clamp N entre 1 et 200, multiplication arrondie à l'entier inférieur. Une porte de pénalité ne tue pas directement le héros.

### 5.2 Armes (valeurs de départ, à équilibrer)
| ID | DPS | Cadence/s | Portée | Particularité |
|---|---:|---:|---:|---|
| pistol | 12 | 2 | 12 | cible unique |
| rifle | 20 | 5 | 15 | cible unique |
| shotgun | 24 | 1 | 7 | 3 cibles maximum, DPS réparti |
| flamethrower | 18 | 4 | 6 | 3 cibles maximum, DPS réparti |
| drone | 16 | 2 | 18 | cible unique, visuel aérien |

MVP : ajouter sniper, minigun, launcher. Compétences de mission : damage +25 %, fire_rate +20 %, range +20 %, armor −20 % dégâts reçus, heal +25 HP immédiats, chain (30 % dégâts sur une seconde cible). Maximum 3 niveaux par compétence ; bonus additifs sauf réduction de dégâts plafonnée à 60 %. XP par élimination : normal 1, rapide 2, tank 5 ; choix à 10/25/45 XP, 3 options distinctes parmi celles disponibles ; si aucune, soin. Choix gratuits, pause, pas de reroll payant. Fusion rifle + chain niveau 2 → Tesla : chaîne sur 2 cibles secondaires à 30 % chacune. Les compétences temporaires sont réinitialisées après mission.

### 5.3 Ennemis
Walker : 20 HP, 4 dégâts/contact, vitesse 2 ; Runner : 12 HP, 3 dégâts, vitesse 5 ; Tank : 90 HP, 12 dégâts, vitesse 1,5 ; Boss Alpha : 600 HP, 20 dégâts, vitesse 1,2, attaque de zone annoncée 1,2 s avant impact toutes les 8 s. Zone rouge lisible sans dépendre de la couleur. Les ennemis ne doivent jamais apparaître directement sur le héros. Maximum 80 ennemis actifs au P0. Boss : phase 2 à 50 % HP, cadence d'attaque accrue de 25 %. Les valeurs sont des hypothèses de tuning.

## 6. Missions et génération
P0 : niveaux authored, pas de génération libre. Chaque mission contient des segments de 20–50 unités, des portes, des vagues, des obstacles et une extraction. M01 tutoriel : 300 unités, portes +5/−2, +10/×2, 20 walkers, recrutement médecin, extraction ; M02 : 450 unités, 35 walkers, 10 runners, ingénieur, obstacle destructible ; M03 : 550 unités, 40 walkers, 15 runners, 4 tanks, scientifique, Boss Alpha. L'ordre des vagues et positions exactes sont définis dans des assets MissionDefinition ; première version à régler en playtest. MVP : 4 missions par zone (ville, forêt, zone industrielle), boss aux missions 6 et 12. Génération P2 : assembler segments compatibles avec seed stable, budget de menace et validation d'au moins une route praticable ; fallback authored si invalide. Pas de difficulté adaptative cachée destinée à provoquer des achats.

## 7. Spécialistes et population
Chaque spécialiste est un personnage unique sauvé une fois. Médecin : soin de 20 HP au prochain départ de mission ; ingénieur : accès atelier et bonus de 10 % DPS permanent ; scientifique : accès laboratoire et compétence chain. Pendant le sauvetage : passer dans une zone d'interaction pendant 0,5 s, avec route alternative de renforts ; pas de sacrifice forcé. Le spécialiste est crédité seulement à la victoire ; répétition ne crée pas de doublon. Population initiale 1 ; +1 par spécialiste unique et +2 civils par première victoire. Les soldats de mission ne deviennent pas automatiquement des habitants. Les grandes populations des visuels sont une vision future, pas un objectif MVP.

## 8. Économie et construction
Deux ressources P0 : scrap (matériaux) et food (vivres). Solde initial 100 scrap, 30 food. Première victoire M01 : 60/20 ; M02 : 80/25 ; M03 : 120/40. Rejouer : 50 % de la récompense fixe, arrondi inférieur. Ramassage : scrap +5, food +3. Pas de génération hors ligne au P0. Aucun solde négatif. Transactions atomiques avec identifiant unique. Les coûts suivants sont des valeurs initiales :
| Bâtiment | Coût scrap/food | Effet | Prérequis |
|---|---|---|---|
| Shelter L1 | 50/10 | +10 capacité | aucun |
| Farm L1 | 60/15 | +5 food par victoire | Shelter |
| Workshop L1 | 80/20 | débloque rifle | ingénieur |
| Clinic L1 | 70/20 | +10 HP max | médecin |
| Laboratory L1 | 100/30 | débloque chain | scientifique |
| Barracks L1 | 90/25 | +2 unités au départ | Shelter |
P0 : Shelter, Farm, Workshop uniquement ; Clinic et Laboratory remplacés par déblocages directs des spécialistes. MVP : 6 bâtiments, niveaux 1–3 ; coût niveau suivant = floor(coût L1 × 1,8^(niveau−1)), bonus de production/capacité proportionnels au niveau, déblocages uniques non cumulables. Construction instantanée, emplacements fixes, aucune attente payante. Capacité initiale 10 ; les recrutements uniques ne sont jamais perdus faute de capacité (capacité temporairement dépassable, affichage explicite). Camp visuel : modèles de bâtiments évoluent à chaque niveau. Les améliorations permanentes sont bornées et configurables.

## 9. UX et direction artistique
Écrans : boot → camp → carte/mission → loadout → combat → choix de compétence → résultats → camp ; pause, paramètres, aide. Première ouverture : démarrer directement M01, tutoriel intégré en 3 instructions (glisser, choisir porte, sauver). HUD : HP, effectif, progression, pause ; zones sûres écran, cibles tactiles ≥48 dp, texte FR/EN, symboles + couleurs pour portes. Pas de popup commercial pendant tutoriel. Options : volume musique/SFX, vibrations, réduction des flashes, qualité low/medium/high, taille de texte. Palette : alliés cyan/bleu, ennemis orange/rouge avec formes distinctes. Style 3D low-poly premium, silhouette lisible, ruines puis végétation et architecture reconstruite. Assets originaux ou licenciés ; aucun logo ou personnage concurrent. Audio : tir, impact, sauvetage, construction, victoire ; limiter superposition des sons. Le visuel conceptuel n'est pas une capture du jeu réel.

## 10. Architecture technique
Choix proposé : Unity 6 LTS (version exacte à verrouiller après vérification de disponibilité), C#, URP, Android ARM64, IL2CPP ; Input System ; Unity Test Framework. Pas de dépendance réseau obligatoire. Utiliser des ScriptableObjects pour données et des classes C# pures pour règles. Scènes Boot, Camp, Mission ; services injectés via composition root, pas de singletons métier dispersés. Assemblies Core (sans UnityEngine), Gameplay, Presentation, Infrastructure, Tests. Pooling ennemis/projectiles/VFX, instancing, LOD, baked lighting, pas d'allocation par frame dans boucle de combat. Cibles proposées : Android 10+, 3 Go RAM, build initial ≤150 Mo, mémoire ≤500 Mo, 30 FPS sur appareil bas de gamme représentatif à définir, 60 FPS sur appareil intermédiaire. Mesurer sur matériel réel, pas seulement éditeur.

Arborescence : Assets/Game/{Core,Gameplay,Presentation,Infrastructure,Content,Art,Audio,Tests}; Docs/; ProjectSettings/; Packages/. Core : CombatRules, GateRules, EconomyRules, MissionStateMachine, SeededRandom, RewardCalculator. Gameplay : PlayerController, SquadController, EnemyDirector, GateTrigger, RescueTrigger, BossController. Infrastructure : SaveRepository, AnalyticsSink, ConfigLoader, Clock. Presentation : HUD, CampView, MissionResultsView, SettingsView. Interfaces : IClock, IRandomSource, ISaveRepository, IAnalyticsSink. États : Boot, Camp, Loading, Running, UpgradeChoice, Paused, Victory, Defeat, Results. Transitions invalides rejetées ; une seule résolution terminale.

### 10.1 Données et sauvegarde
PlayerSave : schemaVersion:int, playerId:string local aléatoire, revision:long, scrap:int, food:int, population:int, specialists:string[], buildings:map<string,int>, unlockedWeapons:string[], completedMissions:string[], appliedTransactions:string[], settings:object. MissionDefinition : id, length, segments[], enemySpawns[], gates[], rescueId, bossId, rewards, seed. WeaponDefinition : id,dps,rate,range,targetCount. GateDefinition : id,x,z,width,operation,value. Sauvegarde JSON locale atomique (temp → validation → remplacement, backup précédent), version et migration testées. En cas de corruption : backup puis écran de récupération explicite ; ne pas écraser silencieusement. Sauvegarder après chaque transaction camp et résolution mission ; mission interrompue reprend au camp sans récompense, ressources permanentes intactes. Pas de données personnelles nécessaires. Ne pas stocker de secrets dans le dépôt. Sauvegarde locale modifiable : acceptable pour solo, non acceptable pour économie compétitive future.

## 11. Analytics et confidentialité
P0 : sink local JSONL avec export de test, sans SDK externe. MVP : fournisseur à choisir après vérification de conformité et consentement requis. Événements : first_open, session_start/end, tutorial_step, mission_start/end (id, résultat, durée, seed, effectif, arme), gate_choice (id, opération, before/after), rescue_attempt/result, upgrade_choice, building_upgrade, economy_transaction, performance_sample, error. Champs communs : event_id, timestamp UTC, session_id, pseudonymous_id, build_version, schema_version. Ne jamais collecter nom, contact, position précise. D1/D7/D30 = proportion d'une cohorte first_open UTC active exactement 1/7/30 jours calendaires plus tard ; dédupliquer utilisateurs, exclure tests internes, documenter biais de consentement. Dashboard : funnel tutoriel, mission par mission, cohortes par build/pays si autorisé, crash-free sessions, FPS. Test initial 1 000 installations ; attendre maturation D30 ; publier intervalles de confiance. À 1 000 joueurs, 40 % D1 a une marge approximative de ±3 points à 95 %. Pas de décision sur une seule cohorte bruitée.

## 12. Monétisation et lancement
P0 sans publicité ni achat. P2 : publicité récompensée facultative après résultats (bonus de matériaux plafonné à 1 par mission), cosmétiques à prix transparent, éventuel achat de suppression si publicités non récompensées introduites ultérieurement. Aucun achat requis pour progresser, aucune fausse rareté, pas de loot boxes payantes. Achats via stores, restauration et validation des reçus avant production. Consentement, politique de confidentialité, classification, règles Google Play/Apple à vérifier au lancement. Créatifs publicitaires capturés depuis build réel. Mesurer CPI, LTV nette, ROAS par cohorte ; scaler seulement si LTV nette dépasse CPI avec marge suffisante et incertitude acceptable.

## 13. Backlog ordonné et critères d'acceptation
| ID | Livrable | Acceptation |
|---|---|---|
| P0-01 | Projet et CI | compilation propre, tests EditMode exécutables, versions verrouillées |
| P0-02 | Contrôle et caméra | glissement borné, pause correcte, 30 Hz stable |
| P0-03 | Tir et ennemis | DPS conforme, cible stable, pool sans fuite |
| P0-04 | Portes et groupe | 1+15=16, 16×3=48, pénalité clamp, activation unique |
| P0-05 | Mission et boss | victoire/défaite uniques, télégraphie visible |
| P0-06 | XP et choix | options distinctes, caps respectés, pause |
| P0-07 | Sauvetage | recrutement à victoire uniquement, aucun doublon |
| P0-08 | Camp et économie | coût exact, fonds insuffisants refusés, effets appliqués |
| P0-09 | Save | redémarrage conserve progression, backup et migration testés |
| P0-10 | UI et tutoriel | parcours complet sans blocage, FR/EN, safe areas |
| P0-11 | Contenu | 3 missions terminables, 5 armes et 3 spécialistes |
| P0-12 | Analytics et build | export valide, APK installable, rapport perf réel |
| P1-01 | Extension contenu | 12 missions, 6 bâtiments, 8 armes, 2 boss |
| P1-02 | Soft launch | cohortes mesurables, conformité et stabilité validées |

## 14. QA et Definition of Done
Unit tests : clamp portes, DPS, réduction dégâts, coûts, récompenses première/replay/défaite, transaction idempotente, spécialiste unique, migrations, RNG stable. PlayMode : tutoriel complet, victoire et défaite, pause/reprise, app background, changement de résolution, sauvegarde après résultats, interruption de mission, navigation sans dead end. Test stress 80 ennemis/40 modèles alliés, 10 minutes, mémoire stable ; profiler sur appareil. Tester low storage, fichier corrompu, mode avion, touches rapides, orientation verrouillée, audio désactivé. DoD : code compilé, tests pertinents réussis, aucun bug bloquant connu, assets licenciés, documentation actualisée, résultats réels consignés. Ne jamais déclarer un test passé sans exécution.

## 15. Risques et décisions ouvertes
Risque principal : combat répétitif malgré la construction ; tester deux variantes (portes dominantes vs choix de builds dominants). Risque secondaire : surcharge UX et économie ; limiter le P0. Autres : performances, coûts d'assets, équilibre, nom de marque, conformité SDK. Décisions ouvertes : équipe/budget, appareil cible exact, version Unity, fournisseurs analytics et ads, calendrier. Estimation indicative P0 : 4–8 semaines pour une petite équipe expérimentée avec assets disponibles ; aucune garantie. Gate P0 : prototype amusant en tests qualitatifs (10–20 personnes) et stable ; Gate P1 : cohortes et KPI ; Gate P2 : rentabilité démontrée. Les visuels et scores du comité sont des hypothèses, non des données mesurées.
