# Charge
# Battery charge tonight

A single-page calculator that answers one question: **what percentage should the home
battery be charged to during the cheap overnight window?**

Built for a specific system — an 18.08 kWh Sigenergy battery with a split east–west
solar array in West Yorkshire, on the Octopus Flux tariff. The numbers are fitted to
that installation and won't transfer to another one without recalculating.

Live at: `https://stevehb1.github.io/charge/`

## Why it isn't just "charge it full"

Flux buys at 14.64p between 02:00 and 05:00 and pays 27.17p for export between 16:00
and 19:00, so overnight charging looks like free money. It isn't, quite.

After round-trip losses a cheap-rate unit costs about **16.2p** delivered. Sold in the
peak it earns 27.17p — an 11p margin, which is real. But if that unit occupies space
the day's solar would have filled for nothing, the displaced solar leaves at 9.69p
instead of 27.17p. That's a 17.5p loss against an 11p gain.

So the rule is: **charge overnight only as far as the solar can't reach.** In December
that's nearly a full battery. In June it's usually nothing at all.

Across a year, following that rather than filling every night is worth roughly £230.

## Using it

Open the page in Safari, then **Share → Add to Home Screen**. It runs full screen with
its own icon and works without a signal.

Enter tomorrow's solar forecast in kWh and read off the percentage. The month is picked
up from the date automatically. The chart underneath shows how much the day earns at
every charge level — it's deliberately flat near the top, so being a few percent out
costs pennies.

If JavaScript is blocked — which happens when iOS previews an HTML file rather than
opening it in a browser — the page falls back to a printed lookup table covering every
month. That table is the whole answer, just without the slider.

### Axle events

Tick the box and enter the start time. Grid-shortage events pay around £1/kWh, roughly
four times the Flux peak rate, so they're worth preparing for.

The overnight charge target **does not change** for an event. What changes is the peak
window: the battery is normally at its floor by 19:00, and it can only refill at 6 kW,
so an event starting before about 20:30 needs energy held back rather than bought later.
The app works out the floor to set and the target to reach.

## Where the numbers come from

Two stages, both from the system's own logs:

1. **Shape** — eight clear days of 5-minute data were fitted to a clear-sky model
   (pvlib, Ineichen clear sky, Hay–Davies transposition). Best fit is a split array,
   roughly 3.75 kW at 25° facing ESE and 3.78 kW at 30° facing WNW.
2. **Size** — 122 days of daily totals (April to August 2026) were compared against that
   model day by day. The clearest days ran 0.97–1.13× the model, averaging 1.05, so
   every clear-day figure carries that calibration.

The recommendation was then back-tested over those 122 real days:

| Strategy | Total | Per day |
|---|---|---|
| This rule | £584 | £4.79 |
| Charging to 100% nightly | £483 | £3.96 |
| Perfect hindsight | £587 | £4.81 |

The rule lands within £3 of knowing the weather in advance. The margin it adds for
forecast error costs about 2p a day — cheap insurance, given that under-charging turns
out to be roughly twice as expensive as over-charging by the same amount.

## Assumptions

- 18.08 kWh usable, 2% minimum state of charge
- 6 kW charge, discharge and export limits
- 95% charge / 95% discharge efficiency (90.3% round trip)
- 0.4 kW background house load
- Import 14.64p / 34.16p / 24.4p, export 4.19p / 27.17p / 9.69p, 61.74p daily standing charge
- Battery covers house load through the peak and from 19:00 to 02:00, exporting the surplus

April to August are anchored to measured output. September to March are the same fitted
array projected forward — real orientation and size, but modelled weather. Winter is the
least trustworthy part.

The model runs about 9% optimistic on export volume against actuals, because it assumes
perfect scheduling and no standby losses. Trust the comparison between strategies more
than the absolute pounds.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole app — HTML, CSS, JS and data in one file, no dependencies |
| `sw.js` | Service worker for offline use. Optional; delete it and the app still works |

To update, upload a new `index.html` over the old one. GitHub Pages republishes in a
minute or two. The service worker fetches the latest copy whenever there's a signal and
falls back to the cache when there isn't.

## Licence

Personal project, no warranty. The tariff rates and system parameters are specific to one
installation — check them against your own before trusting any number here.
