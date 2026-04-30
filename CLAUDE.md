# Contexte du projet — pour les futures sessions Claude

> Ce fichier donne à Claude le contexte nécessaire pour reprendre l'aide
> sur ce projet sans repartir de zéro. Le contenu vient d'une session
> antérieure intensive (préparation interview Flutter + démarrage de Téranga Eats).

---

## L'utilisateur

- **Mouhamadou** (mnchifi@gmail.com / mouhamadou.niang@epitech.eu)
- GitHub : https://github.com/mouhamadoudou
- **Niveau Flutter** : Mid en apprentissage rapide. Solide sur les concepts (Stateless/Stateful, lifecycle, Provider, Clean Architecture, immutability, copyWith, DI). Encore fragile sur l'exécution sous pression (naming, syntaxe, oublis ponctuels).
- **Stack par ailleurs** : React, Angular, Node, TypeScript, Express, MongoDB, PostgreSQL, Python, PHP, C, Erlang, Elixir
- **Préférences pédagogiques** :
  - Préfère taper le code lui-même (mémoire musculaire) plutôt que copier-coller
  - Veut comprendre le **pourquoi**, pas juste le **comment**
  - Répond en **français**
  - N'aime pas qu'on lui pose 10 questions à la suite, préfère du concret

## Contexte interview

- Entretien tech Flutter passé le **2026-04-27** (référé par le CTO au tech lead d'une boîte qui a un besoin Flutter mais pas de poste officiellement ouvert)
- Le tech lead a demandé "un projet à regarder ensemble" → ce repo (Téranga Eats) est exactement la réponse
- Tech lead doit recontacter "bientôt", contact maintenu via WhatsApp
- Plan de relance : J+10/14 si silence (~7-9 mai)
- En parallèle, l'utilisateur cherche d'autres opportunités remote (marché tendu en France)

## Le projet — Téranga Eats

App Flutter de **food delivery** pour des restaurants sénégalais à Dakar.

### Stack

- Flutter / Dart
- [`provider`](https://pub.dev/packages/provider) pour le state management (ChangeNotifier)
- [`http`](https://pub.dev/packages/http) pour les appels API — **HTTP brut volontairement**, pas le SDK `supabase_flutter`
- [`shared_preferences`](https://pub.dev/packages/shared_preferences) pour la persistance locale
- **Supabase** comme backend (PostgreSQL + REST API + RLS)

### Pourquoi HTTP brut au lieu du SDK Supabase

Choix pédagogique délibéré : l'utilisateur n'a jamais fait d'HTTP en Flutter. Utiliser le SDK cacherait tout. Avec `http` brut on apprend :
- Construction d'`Uri`, headers (apikey, Authorization)
- Sérialisation `fromJson` / `toJson`
- Gestion d'erreur (status code, timeout)
- Repository pattern qui prend tout son sens (impl swap)

### Repository GitHub

https://github.com/mouhamadoudou/teranga-eats

## Architecture

**Clean Architecture stricte** par feature :

```
lib/
├── features/
│   ├── restaurants/
│   │   ├── domain/        # Entités + interface repository (Dart pur, aucun import Flutter)
│   │   ├── data/          # Implémentations concrètes (HttpRestaurantRepository, etc.)
│   │   └── presentation/  # ViewModels (ChangeNotifier) + Widgets
│   ├── cart/
│   └── orders/
├── core/
│   ├── env.dart           # GITIGNORED — clés Supabase
│   ├── env.example.dart   # versionné, template
│   └── http/              # singleton client HTTP partagé
└── main.dart
```

**Règle d'or Clean Architecture** : les dépendances pointent vers `domain`. Domain ne dépend de rien.

## Backend Supabase

- Projet : `ewbbitrxpdfmemqlbfyg.supabase.co`
- Tables : `restaurants`, `menu_items` (FK → restaurants), `orders` (jsonb items)
- RLS activée. Policies en V1 :
  - `restaurants` / `menu_items` : lecture publique (anon)
  - `orders` : lecture + insert publics (anon)
- Données seed actuelles : 3 restaurants (Chez Loutcha, La Calebasse, Layu), 3 menu_items
- ⚠️ Les données ont des `\t` parasites en fin de string (à nettoyer dans le Table Editor Supabase)

## V1 scope (en cours)

Côté **client uniquement**. Pas d'auth en V1.

- [ ] Liste restaurants depuis Supabase (REST GET)
- [ ] Écran détail restaurant + menu items
- [ ] Panier local (Provider, pas persisté pour V1)
- [ ] Validation commande → POST sur table `orders`
- [ ] Historique commandes → GET sur `orders`

## V2 / V3 (plus tard, hors scope V1)

- Auth Supabase (côté client + côté restaurant)
- Onboarding restaurants (créer compte, ajouter menu)
- Real-time delivery tracking
- Vrai paiement (Stripe / mobile money)
- Notifications push

## Conventions du repo

- **Fichiers** : `snake_case.dart`
- **Classes** : `PascalCase`
- **Variables / méthodes** : `camelCase`
- **Privé** : préfixe `_`
- **Conventional commits** :
  - `feat:` nouvelle feature
  - `fix:` bug fix
  - `docs:` documentation
  - `refactor:` refacto sans changement de comportement
  - `chore:` config / dépendances
  - `test:` ajout de tests
- **Commits petits et atomiques** (1 concept par commit)
- Branches feature recommandées (`feature/restaurant-list`, etc.)

## Points d'attention récurrents de l'utilisateur

À garder en tête quand on review son code :

1. **Naming** : tendance à écrire `Counterstate` ou `counterstate` au lieu de `CounterState`. Vérifier systématiquement le PascalCase strict.
2. **`dispose()`** : oublie d'appeler `.dispose()` sur les `TextEditingController`. A déjà écrit `_controller;` dans dispose au lieu de `_controller.dispose();`.
3. **State vs Repository** : confusion classique. Repository ≠ State. Le repo manipule un state, il ne L'EST pas. Surveiller les noms incohérents (`InMemoryShoppingState` qui devrait être `InMemoryShoppingRepository`).
4. **Mutation de listes immutables** : tendance à `_state.products.add(x)` au lieu de `_state = _state.copyWith(products: [..._state.products, x])`.
5. **Provider** : oublie parfois `notifyListeners()` après mutation. Bien vérifier dans chaque méthode du ViewModel.
6. **`const`** : à rappeler de mettre `const` sur tous les widgets compatibles, et sur `CounterState.initial()` quand utilisé.
7. **ListView dans Column** : rappel classique de wrapper avec `Expanded`.

## Setup local (pour un nouveau dev)

```bash
git clone https://github.com/mouhamadoudou/teranga-eats.git
cd teranga-eats
cp lib/core/env.example.dart lib/core/env.dart
# remplir les clés Supabase dans env.dart
flutter pub get
flutter run -d chrome
```

## Style d'aide attendu

- Pour chaque erreur de l'utilisateur : expliquer le **pourquoi** avant le fix
- Préférer **guider** plutôt que coller la solution complète (sauf si bloqué après 2 essais)
- Petites étapes incrémentales, validation à chaque étape
- Ne pas surcharger d'infos d'un coup
- L'utilisateur préfère les réponses **directes**, pas les longs préambules
