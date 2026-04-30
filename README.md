# Téranga Eats

Application Flutter de commande dans des restaurants sénégalais.
Backend Supabase, architecture Clean (domain / data / presentation).

## Status

🚧 En développement — V1 client uniquement, données mockées sur Supabase.

## Stack

- Flutter / Dart
- [provider](https://pub.dev/packages/provider) pour le state management
- [http](https://pub.dev/packages/http) pour les appels API
- [shared_preferences](https://pub.dev/packages/shared_preferences) pour la persistance locale
- [Supabase](https://supabase.com) comme backend (PostgreSQL + REST API)

## Architecture

Clean Architecture en 3 couches par feature :

​```
features/{feature}/
├── domain/        # Entités + interfaces (Dart pur)
├── data/          # Implémentations concrètes (HTTP, mock)
└── presentation/  # ViewModels (ChangeNotifier) + Widgets
​```

## Setup local

1. Cloner le repo
2. Créer `lib/core/env.dart` à partir de `lib/core/env.example.dart` avec tes propres clés Supabase
3. `flutter pub get`
4. `flutter run`

## Roadmap

### (en cours)
- [ ] Liste des restaurants depuis Supabase
- [ ] Détail restaurant + menu
- [ ] Panier local (Provider)
- [ ] Validation commande → POST sur Supabase
- [ ] Historique des commandes
