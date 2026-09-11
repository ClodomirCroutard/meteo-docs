# Veille Météo — Collecte multi-sources pour Rennes

Collecte les données météo de Rennes depuis 3 sources indépendantes (open-meteo, wttr.in, met-norway), calcule des moyennes, génère un récap horaire (maintenant, +1h, journée suivante), publie les données brutes et le récap dans un repo GitHub dédié, et envoie le récap sur Discord. Fonctionne en mode une fois ou en boucle toutes les 4 heures.

_Documentation générée automatiquement le 11/09/2026 à 16:21_

## Architecture

- **`collectors/weather_sources/open_meteo.py`** — Source principale, API sans clé, météo horaire précise
- **`collectors/weather_sources/wttr_in.py`** — Source secondaire, mapping des codes météo propres vers WMO
- **`collectors/weather_sources/met_no.py`** — Source tertiaire, météo norvégienne (nécessite User-Agent)
- **`collectors/weather_collector.py`** — Orchestrateur + agrégation (moyennes sur les 3 sources)
- **`publishers/weather_publisher.py`** — Publie récap + données brutes dans le repo GitHub
- **`communicators/discord_sender.py`** — Envoie le récap sur Discord (#Rapports)

```
open-meteo  ─┐
wttr.in     ─┤→ WeatherCollector (agrégation) → moyennes
met-norway  ─┘
                  ↓
           WeatherPublisher → GitHub (veille-meteo-rennes)
                  ↓
           DiscordSender → Discord #Rapports
```

## Variables d'environnement

| Variable | Description |
|---|---|
| `WEATHER_CITY` | Ville cible (défaut: `Rennes`) |
| `WEATHER_LATITUDE` | Latitude (défaut: `48.1173`) |
| `WEATHER_LONGITUDE` | Longitude (défaut: `-1.6778`) |
| `WEATHER_TIMEZONE` | Fuseau horaire (défaut: `Europe/Paris`) |
| `WEATHER_SOURCES` | Sources activées (défaut: `open-meteo,wttr.in,met-norway`) |
| `WEATHER_INTERVAL_HOURS` | Intervalle de la boucle en heures (défaut: `4`) |
| `WEATHER_REPO` | Nom du repo GitHub (défaut: `veille-meteo-rennes`) |
| `WEATHER_REPO_PRIVATE` | `true` pour repo privé |
| `WEATHER_DATA_PATH` | Sous-dossier pour les données brutes (défaut: `data`) |
| `WEATHER_LINGUA` | Langue des libellés (défaut: `fr`) |

## Commandes

- **`meteo`** — Lance une collecte unique + publie sur GitHub + Discord
- **`python main.py --meteo`** — Idem, en ligne de commande
- **`python main.py --meteo-watch`** — Boucle toutes les N heures
- **`run_meteo_watch.ps1`** — Boucle Windows avec auto-restart en cas de crash

## Lancement

```bash
# Lancer le mode souhaité (voir commandes ci-dessus)
python main.py --listen   # mode écoute Discord
python main.py pipeline   # mode pipeline (top-data)
python main.py --meteo    # une fois (météo)
python main.py --meteo-watch  # boucle météo
python main.py --follow-watch # boucle watcher GitHub
```

---
_Fait avec courage par l'agent data collector._
