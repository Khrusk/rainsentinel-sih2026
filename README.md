# RainSentinel

**From rain clouds to street-level flood warnings.**

An AI/ML-based integrated heavy-rainfall early-warning and inundation prediction system that fuses satellite, radar, rain-gauge and NWP data to forecast which wards and streets will flood, and sends impact-based alerts.

> **Smart India Hackathon 2026** · Problem Statement **SIH26071** · Theme: Disaster Management · Category: Software
> **Team Kasukabe MNIT** · Team ID **178633**

**Status:** idea stage, prototype in progress. The screens in the presentation are UI concepts with simulated data, not measured results.

---

## The problem

Forecasts tell us *how much* rain will fall, not *where water will collect, how deep, and when*. In Mumbai on 26 July 2005, 944 mm fell in 24 hours, with 500+ deaths and about ₹2,000 crore in losses. India now has 47 Doppler radars (about 87% coverage) and 6,726 rain-gauge stations, but satellite, radar, gauge and NWP data are mostly used in silos, and the last mile to a street-level warning is missing.

## The solution

RainSentinel joins four data streams into one pipeline:

```
 SENSE  ->  NOWCAST  ->  FUSE  ->  INUNDATE  ->  WARN
```

| Stage | What it does |
|---|---|
| **Sense** | Ingests INSAT satellite, DWR radar, AWS/rain-gauge and NWP data (GFS, IMD models) |
| **Nowcast (0-3 h)** | Deep-learning rainfall nowcast from radar and satellite (ConvLSTM / U-Net) |
| **Fuse (3-72 h)** | Blends nowcast with NWP, ML bias correction, ensemble/quantile uncertainty |
| **Inundate** | ML surrogate of a hydrodynamic model gives ward- and street-level flood-depth maps in seconds |
| **Warn** | Impact rules and geofences produce CAP 1.2 alerts over push, SMS and IVR; a human approves before dispatch |

Example alert: *"Kurla West likely to flood in 40 min, 0.6-0.9 m. Avoid the underpass, move vehicles to higher floors."*

## What makes it different

- **Four streams, one model**: satellite, radar, gauges and NWP fused, not used in silos
- **Rain to flood in one pipeline**: continues past rainfall to street-level depth
- **Impact-based, not intensity-based**: says what will happen and what to do
- **Trust by design**: probabilistic outputs, source and time on every map, human-approved alerts
- **Standards-based**: CAP 1.2 plugs into existing IMD and SDMA alerting

## Architecture

```
 INSAT satellite ─┐
 DWR radar ───────┤   Ingest + QC     AI core                Inundation          Alerts + apps
 AWS + gauges ────┼─> (pollers,   ─>  (nowcast 0-3 h,   ─>   (ML flood      ─>  (impact rules,
 NWP models ──────┘   xarray/Zarr)    NWP correction,        surrogate,          CAP 1.2, push/SMS/IVR,
                                      uncertainty)           DEM + drains)       web console, citizen app)
```

## Tech stack

Python · PyTorch · xarray + Zarr · FastAPI · PostgreSQL + PostGIS · Redis · React + MapLibre · Docker + Kubernetes

## Design targets (to validate in a pilot)

| Target | Value |
|---|---|
| AI nowcast lead time | 0-3 h |
| NWP with ML correction | 3-72 h |
| Nowcast to alert dispatch | < 60 s |
| Flood-map grid resolution | 10-30 m |

These are goals, not measured results.

## Roadmap

- [ ] **Phase 1 - Hackathon MVP (36 h):** one-city radar + satellite nowcast, ward flood map, alert console
- [ ] **Phase 2 - Pilot (3-6 months):** validate on past storms with a municipal body and IMD; tune false alarms
- [ ] **Phase 3 - Scale (6-12 months):** more cities, new Mission Mausam radars, alerts in 22 languages

## Planned repository structure

```
rainsentinel/
├── data/          # ingest pollers, QC, common-grid builders
├── models/        # nowcasting, NWP correction, flood surrogate
├── api/           # FastAPI services
├── web/           # React + MapLibre command console and citizen app
├── alerts/        # CAP 1.2 generator and dispatch
├── docs/          # presentation, diagrams
└── README.md
```

*(Folders will be added as the prototype is pushed.)*

## Getting started

Setup instructions will be added once the prototype code is pushed to this repository.

## Team

**Team Kasukabe MNIT** (Team ID 178633), Malaviya National Institute of Technology, Jaipur

## References

1. Deccan Herald, *A reminder of the 2005 deluge* (944 mm in 24 h; 500+ dead; ₹2,000 cr)
2. MoES / PIB, *Upgradation in observation network*, Rajya Sabha (29 Jan 2026)
3. UN / WMO, *Early Warnings for All: Fast Facts* (2025)
4. WMO, *About Early Warnings for All*
5. World Bank, *What states can learn from Odisha in disaster preparedness* (2019)
6. Ravuri et al., *Skilful precipitation nowcasting using deep generative models of radar*, Nature 597 (2021). https://www.nature.com/articles/s41586-021-03854-z
7. Shi et al., *Convolutional LSTM Network for precipitation nowcasting*, NeurIPS (2015). https://arxiv.org/abs/1506.04214
8. Nevo et al., *Flood forecasting with machine learning in an operational framework*, HESS 26 (2022). https://hess.copernicus.org/articles/26/4013/2022/

## License

Add a license (for example MIT) before making the repository public, or remove this section.
