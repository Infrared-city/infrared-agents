# Climate Simulation & Analysis AI Agents — Landscape Research

**Date:** 2026-04-02
**Status:** Complete
**Goal:** Survey existing AI tools for urban climate analysis (UHI, wind, solar, flood, energy) to position our climate agent skills.

---

## Key Finding: No Agent-Native Climate Platform Exists

All existing climate simulation tools are **GUI-first** (Forma, ENVI-met, Orbital Stack). None expose an agent-friendly API where an LLM can orchestrate simulation runs, interpret results, and suggest design changes conversationally. **This is the gap.**

---

## Commercial Microclimate & Environmental Simulation

### Autodesk Forma (formerly Spacemaker AI)
- **URL:** [archtech.se coverage](https://www.archtech.se/2025/03/22/spacemaker-is-now-autodesk-forma/)
- **What:** Cloud-based platform evaluating designs against 100+ environmental criteria: sunlight, wind, noise, microclimate, solar potential, view analysis. Generative design engine suggests optimal building layouts.
- **Platform:** Cloud SaaS (browser)
- **Commercial** (Autodesk subscription)
- **Relevance:** VERY HIGH — direct competitor in environmental analysis for urban design. The 800-lb gorilla. Key difference: Forma is a design tool with analysis, not an agent-based system.

### ENVI-met
- **URL:** [envi-met.com](https://envi-met.com/microclimate-simulation-software/)
- **What:** Gold standard for 3D microclimate simulation. 3,000+ independent studies. Recently integrated AI-based object detection for UHI analysis + partnered with Google AI for heat mitigation. Models shortwave/longwave radiation, wind, thermal comfort.
- **Platform:** Desktop (Windows), cloud processing
- **Commercial** (acquired by One Click LCA)
- **Relevance:** VERY HIGH — most validated microclimate model. Infrared could position agents as faster AI-native alternative, or as orchestration layer calling ENVI-met as backend.

### Orbital Stack (by RWDI)
- **URL:** [orbitalstack.com](https://orbitalstack.com/)
- **What:** AI + CFD web-based microclimate analysis from world's leading wind engineering consultancy. Wind comfort, thermal comfort, pedestrian safety. Freemium starting ~$249/mo.
- **Platform:** Cloud SaaS (browser)
- **Commercial**
- **Relevance:** VERY HIGH — closest competitor for cloud-based urban climate analysis. Infrared differentiator: agent/conversational interface + deeper climate adaptation focus.

### Digital Blue Foam (DBF)
- **URL:** [digitalbluefoam.com](https://www.digitalbluefoam.com/)
- **What:** Singapore SaaS combining AI generative design + sustainability analytics (wind, solar radiation, shadow, daylight autonomy, carbon). Claims 90% time savings on feasibility studies.
- **Platform:** Cloud SaaS
- **Commercial**
- **Relevance:** HIGH — directly competes in "early-stage design + sustainability analysis." Agent-based approach differentiates Infrared.

---

## Open Source Climate/Simulation Tools

### Ladybug Tools (LBT)
- **URL:** [ladybug.tools](https://www.ladybug.tools/)
- **What:** Free, open-source environmental analysis for Rhino/Grasshopper. Ladybug (climate data), Honeybee (energy/daylighting), Dragonfly (urban-scale energy), Butterfly (CFD). 500K+ downloads. Connects to EnergyPlus, Radiance, OpenFOAM.
- **Platform:** Rhino/Grasshopper plugin
- **Open source** (GPL)
- **Relevance:** VERY HIGH — the open-source ecosystem Infrared agents could wrap or call. An agent orchestrating Ladybug analyses via natural language would be extremely powerful.

### SynxFlow
- **URL:** [github.com/SynxFlow/SynxFlow](https://github.com/SynxFlow/SynxFlow)
- **What:** GPU-accelerated Python package for flood, landslide, debris flow simulation. `pip install synxflow`. Used in flood early warning + insurance mapping. City-scale sims in minutes on GPU.
- **Platform:** Python (CUDA/SYCL GPU)
- **Open source** (JOSS published)
- **Relevance:** VERY HIGH — directly relevant to Infrared's flood risk product. Could be wrapped as simulation backend for an Infrared flood agent.

### ML Surrogate Flood Models
- **What:** Multiple research groups have built CNN-based surrogate models producing high-resolution flood inundation maps in <10 seconds (vs hours for physics-based). Train on TUFLOW/LISFLOOD outputs, deploy lightweight CNN for real-time prediction.
- **Platform:** Research code (PyTorch/TensorFlow)
- **Open source:** Various
- **Relevance:** VERY HIGH — exactly the approach for real-time flood risk in an agent context. Physics simulation too slow for interactive use; ML surrogates are the path.

---

## Climate Risk Platforms

### UrbanFootprint (acquired by LightBox, June 2025)
- **URL:** [urbanfootprint.com](https://urbanfootprint.com/)
- **What:** Resilience decision intelligence covering 160M US land parcels. Climate hazard, community vulnerability, infrastructure data. Three foundations: hazard risk, community characteristics, land use/buildings.
- **Platform:** Cloud SaaS + API (AWS Marketplace)
- **Commercial**
- **Relevance:** MEDIUM — data/analytics platform, not simulation. Complementary rather than competitive.

### One Concern
- **URL:** [oneconcern.com](https://oneconcern.com/en/)
- **What:** AI-powered climate risk intelligence. Physics + ML for floods, earthquakes, wildfires, sea-level rise, extreme heat. Building "digital twin of world's natural and built environments." Resilience-as-a-Service.
- **Platform:** Cloud SaaS + API
- **Commercial**
- **Relevance:** HIGH — "Resilience-as-a-Service" concept analogous to what Infrared agents could offer. They focus on asset-level risk; Infrared differentiates with design-phase intervention.

### Google Environmental Insights Explorer / Project Sunroof
- **URL:** [sunroof.withgoogle.com](https://sunroof.withgoogle.com/data-explorer/)
- **What:** ML on satellite imagery for rooftop solar potential, tree canopy, building/transport emissions. Free for cities.
- **Platform:** Web + API
- **Free / Commercial** (Solar API is paid)
- **Relevance:** MEDIUM — data source for Infrared agents, not a competitor.

---

## Digital Twin Platforms (Adjacent)

### Cityzenith SmartWorldOS
- **URL:** [cityzenith.com](https://cityzenith.com/)
- **What:** Digital twin for buildings, cities, infrastructure. Claims 35% operating cost reduction, 50-100% carbon reduction. NYC, LA, Phoenix, Houston.
- **Relevance:** MEDIUM — building/portfolio operations level, not design-phase.

### Hexagon Urban Digital Twin
- **URL:** [hexagon.com/urban-digital-twin](https://hexagon.com/go/sig/urban-digital-twin)
- **What:** Three-layer architecture (digital/connected/intelligent city). IoT integration (air/water quality, temperature). OGC standards. Partners: Stuttgart, Zug.
- **Relevance:** MEDIUM — enterprise infrastructure focus.

### UrbanistAI
- **URL:** [site.urbanistai.com](https://site.urbanistai.com/)
- **What:** Participatory planning + AI co-design. Desktop, iPad, mobile. Map-based campaigns for public space reimagining. Custom generative AI.
- **Relevance:** LOW-MEDIUM — public engagement focus, not technical simulation.

---

## Sources
- [ENVI-met](https://envi-met.com/microclimate-simulation-software/)
- [Orbital Stack](https://orbitalstack.com/)
- [Digital Blue Foam](https://www.digitalbluefoam.com/)
- [Ladybug Tools](https://www.ladybug.tools/)
- [SynxFlow GitHub](https://github.com/SynxFlow/SynxFlow)
- [UrbanFootprint](https://urbanfootprint.com/)
- [One Concern](https://oneconcern.com/en/)
- [AI for Climate Impacts: Flood Risk (Nature)](https://www.nature.com/articles/s41612-023-00388-1)
