# Product

## The problem

Heat stress costs dairy farmers money before it is visible. As temperature and humidity rise together, cattle eat less, drink more, spend energy on cooling instead of milk production, and in severe conditions face genuine health risk.

The metric that quantifies this is the **Temperature–Humidity Index (THI)** — a standard measure in commercial dairy science. It matters because humidity, not temperature alone, is what determines whether an animal can cool itself. A hot, humid day is far more dangerous than a hot, dry one at the same temperature, and a thermometer alone will not tell you which one you are having.

The scale the app uses:

| THI | Tier | What it looks like |
|---|---|---|
| Below 68 | Comfort | Normal eating, normal rest, steady yield |
| 68 – 71 | Mild stress | Eats less, seeks shade, yield dips slightly |
| 72 – 79 | Moderate stress | Faster breathing, sweating, drinking more, lying down less |
| 80 – 89 | Severe stress | Pronounced signs, substantial yield loss |
| 90+ | Danger | Serious health risk |

Guidance in the app pairs each tier with what to actually do — move to shade, wet the legs and udder, add airflow, shift feeding to cooler hours — rather than stopping at a number.

---

## Who it's for

Small and mid-sized dairy farmers in India, and the veterinary and farm-health professionals who advise them.

This audience is specific in ways that shaped nearly every decision:

- They have a smartphone. They do not have a weather station, a sensor array, or a hardware budget.
- Many read Hindi or Gujarati more comfortably than English, on a phone whose system language may be set to English regardless.
- Connectivity is often poor and data is not free.
- Paying for an app is not a default behaviour, and the price point that works in San Francisco is meaningless here.

The tools that measure THI properly already exist — integrated weather-station sensors and precision livestock monitoring platforms. They are built for large commercial operations. The gap is not knowledge; it is access.

---

## Product decisions, and why

**Language chosen on first run, not inherited from the system.** A farmer's phone may be set to English because someone else configured it. The app asks directly, in all three scripts, before anything else happens.

**Guidance, not just a number.** "THI 79" means nothing to most users. "Moderate stress — wet the legs, udder and back with cool water two to three times a day, add airflow, feed during cooler hours" is actionable. Every tier carries both the symptoms to watch for and the steps that help.

**Breed awareness.** Indigenous breeds (Gir, Sahiwal, Kankrej) tolerate heat better than crossbred and exotic breeds (Holstein, Jersey) and show signs at somewhat higher THI. The guidance says so, because advice that ignores this is wrong for a large share of the audience.

**Multiple sheds.** Cattle kept in more than one location experience different conditions. Each shed carries its own location, its own live reading and its own history.

**Manual entry.** For users with their own thermometer, or without connectivity when they need a reading.

**Background alerts.** The app is useful precisely when nobody is looking at it. Monitoring runs in the background and notifies on dangerous conditions, rate-limited so a hot week doesn't become a stream of identical alerts.

**Priced for the audience.** A free tier with ads, a 14-day trial, and subscriptions at ₹49 monthly, ₹149 for six months, or ₹299 annually. The free tier is genuinely usable rather than a crippled demo, because an app that a farmer cannot evaluate is an app they will not buy.

---

## What it deliberately is not

**Not a diagnostic tool.** It reports environmental heat-stress risk and typical associated signs. It does not diagnose individual animals and does not replace a veterinarian.

**Not precision agriculture.** No sensors, no per-animal tracking, no herd management. The entire premise is that the useful 80% is reachable with a phone and a weather API.

**Not built for scale it doesn't have.** No backend, no accounts, no cloud sync. Those would add cost and failure modes without improving the thing the user actually opens the app to do.

Milk-loss figures shown in the app are described as approximate and varying with breed, feed and available cooling — because they do, and stating them as precise would be misleading.
