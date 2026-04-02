# Urban Planning & Design AI Agent Plugins — Landscape Research

**Date:** 2026-04-02
**Status:** Complete
**Goal:** Survey existing AI agent tools/plugins for urban planning and design to inform our own approach.

---

## Key Finding: No Dedicated AI Coding Assistant Plugins Exist

There are **zero** Claude Code skills, GPT plugins, or Cursor extensions specifically targeting urban planning or climate simulation as of April 2026. The field is dominated by standalone commercial platforms, research prototypes, and traditional CAD/GIS plugins. **This is the gap.**

---

## LLM-Based Urban Planning Agents

### Urban-GPT-Alpha (Studio Tim Fu)
- **URL:** [parametric-architecture.com coverage](https://parametric-architecture.com/urban-gpt-alpha-studio-tim-fu/)
- **What:** Converts plain-language prompts into 3D urban layouts by combining ChatGPT + diffusion models within Rhino/Grasshopper. Users type a design directive and see results in real time.
- **Platform:** Rhino/Grasshopper plugin
- **Status:** Research/prototype
- **Relevance:** HIGH — demonstrates "chat-to-urban-form" agent concept, but no climate simulation layer.

### PlanGPT (Peking University / China Academy of Urban Planning)
- **URL:** [arXiv paper](https://arxiv.org/html/2402.19273v1)
- **What:** First specialized LLM framework for urban/spatial planning. Custom embedding model (Plan-Emb) fine-tuned on planning documents. PlanAgent queries networks, visual aids, charts, domain models. Scored 86.67 on human evaluation vs. ChatGPT's 58.0.
- **Platform:** Research prototype (Python)
- **Open source:** Partially (paper + code)
- **Relevance:** HIGH — domain-specific fine-tuning on planning documents dramatically outperforms general LLMs.

### CityGPT (HKUST)
- **URL:** [arXiv paper](https://arxiv.org/html/2406.13948)
- **What:** Enhances LLM urban spatial cognition via self-weighted fine-tuning (SWFT). Includes CityEval benchmark: city image understanding, urban semantics, spatial reasoning, composite tasks.
- **Platform:** Research framework
- **Open source:** Yes (GitHub)
- **Relevance:** HIGH — spatial cognition capabilities needed for reasoning about urban geometry + climate interactions.

### GIS Copilot (QGIS + LLM)
- **URL:** [tandfonline.com](https://www.tandfonline.com/doi/full/10.1080/17538947.2025.2497489)
- **What:** Embeds LLMs into QGIS to autonomously generate spatial analysis workflows from natural language.
- **Platform:** QGIS plugin
- **Relevance:** MEDIUM — shows how to integrate LLM agents into existing GIS tools.

### GeoColab (Multi-Agent Framework)
- **URL:** [tandfonline.com](https://www.tandfonline.com/doi/full/10.1080/17538947.2025.2569405)
- **What:** First multi-agent framework for geospatial code generation. Addresses code hallucination. Supports climate change response, urban structure analysis, disaster risk, social equity.
- **Platform:** Python framework
- **Open source:** Yes
- **Relevance:** HIGH — multi-agent architecture for geospatial tasks directly applicable to Infrared.

### EuclidHL (Esri)
- **URL:** [ArcNews](https://www.esri.com/about/newsroom/arcnews/gis-based-large-language-model-answers-city-planning-questions/)
- **What:** AI planning/zoning assistant using GIS + generative AI + LLMs. Answers planning questions with maps and graphics.
- **Platform:** Esri ArcGIS ecosystem
- **Commercial**
- **Relevance:** MEDIUM — more zoning compliance than simulation, but demonstrates conversational planning assistant pattern.

### LLM-Geo
- **URL:** [github.com/gladcolor/LLM-Geo](https://github.com/gladcolor/LLM-Geo)
- **What:** Autonomous GIS agent framework for geospatial data retrieval using LLMs.
- **Platform:** Python / GitHub
- **Open source:** Yes
- **Relevance:** MEDIUM — reference for building data retrieval agents in GIS domain.

---

## Curated Research Collections

### Awesome-Urban-LLM-Agents (HKUST)
- **URL:** [github.com/usail-hkust/Awesome-Urban-LLM-Agents](https://github.com/usail-hkust/Awesome-Urban-LLM-Agents)
- **What:** Curated list of urban-focused LLM agent projects: traffic agents (LLMLight, TrafficGPT), mobility agents (AgentMove, TrajAgent), spatial reasoning (CityGPT, Spatial-RAG).
- **Relevance:** HIGH — canonical list of everything at the intersection of LLMs and urban systems.

### Awesome-Urban-Foundation-Models (HKUST)
- **URL:** [github.com/usail-hkust/Awesome-Urban-Foundation-Models](https://github.com/usail-hkust/Awesome-Urban-Foundation-Models)
- **What:** Collection of Urban Foundation Models pre-trained on multi-source urban data — transportation, planning, energy, environment, safety.
- **Relevance:** HIGH — maps the entire space of foundation models for urban computing.

### AAAI 2025 Workshop: AI for Urban Planning
- **URL:** [ai-for-urban-planning.github.io/AAAI25-workshop](https://ai-for-urban-planning.github.io/AAAI25-workshop/)
- **What:** Dedicated AAAI workshop. Signals the field's maturation.

---

## Generative Urban Design (Commercial)

### TestFit
- **URL:** [testfit.io](https://www.testfit.io/)
- **What:** Real estate feasibility with generative design. Generates thousands of site solutions in seconds. "Urban Planner" for large-scale massing. Multi-family, industrial, mixed-use.
- **Relevance:** MEDIUM — focuses on economics/unit counts, not climate. Complementary.

### Giraffe
- **URL:** [giraffe.build](https://www.giraffe.build/)
- **What:** Browser-based mapping + sketching + live analytics. AI-driven generative design with GIS context. Free tier; paid from $45/mo.
- **Relevance:** MEDIUM — urban design tool with light analytics, no climate simulation depth.

### ArchiVinci
- **URL:** [archivinci.com/ai-urban-design](https://www.archivinci.com/ai-urban-design)
- **What:** AI masterplan generator. Transforms black-and-white site plans into colored masterplans.
- **Relevance:** LOW — rendering/visualization tool, not simulation.

### Esri ArcGIS CityEngine
- **URL:** [esri.com CityEngine](https://www.esri.com/en-us/arcgis/products/arcgis-cityengine/overview)
- **What:** Procedural 3D city modeling. Python 3 API (2025.1). Experimental generative AI for urban planning: AI assistant generates massing models applying best practices.
- **Relevance:** MEDIUM — strong 3D modeling. Infrared agents could consume CityEngine outputs.

---

## Open Source Urban AI

### DRL Urban Planning (Tsinghua FIB-Lab)
- **URL:** [github.com/tsinghua-fib-lab/DRL-urban-planning](https://github.com/tsinghua-fib-lab/DRL-urban-planning)
- **What:** Deep reinforcement learning for spatial layout of land use and roads. Published in Nature Computational Science. Graph neural network generates layouts with superior spatial efficiency. Human-AI collaborative workflow.
- **Relevance:** HIGH — state-of-the-art AI-driven spatial planning. Could be an optimization backend.

### Open Urban Planning Toolbox (IDB / EL-BID)
- **URL:** [github.com/EL-BID/open-urban-planning-toolbox](https://github.com/EL-BID/open-urban-planning-toolbox)
- **What:** Suite: MAIIA (informal settlement detection from satellite), URSA (satellite urban analysis), UrbanPy (population estimation, POI, travel times).
- **Relevance:** MEDIUM — satellite analysis tools that could feed data into Infrared agents.

### UrbanSim
- **URL:** [urbansim.com](https://www.urbansim.com/)
- **What:** Simulates interactions between land use, transportation, economy, environment. 20+ year platform. 81.8M people in regions with UrbanSim-informed plans.
- **Relevance:** MEDIUM — regional/metropolitan scale, more macro than Infrared's site-level focus.

---

## Sources
- [PlanGPT arXiv](https://arxiv.org/html/2402.19273v1)
- [CityGPT arXiv](https://arxiv.org/html/2406.13948)
- [Awesome-Urban-LLM-Agents](https://github.com/usail-hkust/Awesome-Urban-LLM-Agents)
- [Awesome-Urban-Foundation-Models](https://github.com/usail-hkust/Awesome-Urban-Foundation-Models)
- [Urban Planning in the Era of LLMs (Nature Computational Science)](https://www.nature.com/articles/s43588-025-00846-1)
