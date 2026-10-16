# Fähigkeitsmatrix: Crawl4AI → StealthBrowser (MODUS B · Zone 1)

**Stand:** 2026-10-16 · **Auftrag:** StealthBrowser um alle gegenüber Crawl4AI fehlenden
Fähigkeiten erweitern — eigenständige, lokal betreibbare Browser-/Crawl-/Extraktionsbasis.
**Dieses Dokument** ist der Crawl4AI-seitige Teil der Matrix (Spalte „Crawl4AI-Quellbeleg")
**vollständig belegt**; die StealthBrowser-Spalte ist aus dieser Session **nicht prüfbar**
(siehe §0.2) und muss in der StealthBrowser-Session nachgetragen werden.

Labels: **[V]** verifiziert am Code · **[E]** extern/relayed (Doc, Auftrag, Hub) · **[A]** Annahme ·
**[R]** Recherche nötig / nicht prüfbar.

---

## 0. Evidenzlage & Coverage-Manifest

### 0.1 Crawl4AI-Quelle [V]
- Repo-Stand: `noerix-dev/crawl4ai` `main` = **`468471a`** (lokaler Fork `noerix-admin/crawl4ai`
  ist nur noch ein Redirect; `git rev-list --left-right --count origin/main...noerixdev/main` = `0 0`).
  Version `crawl4ai/__version__.py` = **0.9.0**. Lizenz **Apache-2.0** (`LICENSE`).
- **Gelesen/erhoben (Methode):** `git ls-files` als Grundmenge; für **jedes** Modul unter
  `crawl4ai/` (ohne `legacy/`) Zeilenzahl + letzter Commit erhoben; **Klassen-/Funktions-
  signaturen** aller Kernmodule extrahiert; **Config-Felder** aus `async_configs.py`
  (`BrowserConfig`, `CrawlerRunConfig`, `HTTPCrawlerConfig`, `LLMConfig`, `SeedingConfig`,
  `VirtualScrollConfig`, `LinkPreviewConfig`, `GeolocationConfig`); **HTTP-Routen** aus
  `deploy/docker/*.py`; **CLI-Kommandos** aus `cli.py`; **MCP-Tools** aus `server.py`;
  **Hooks** aus `async_crawler_strategy.py`; **Tests** als Verzeichnis-/Datei-Liste (154 Dateien);
  **Doku-Themen** als Dateiliste `docs/md_v2/{core,advanced,extraction}`;
  `deploy/docker/ARCHITECTURE.md` vollständig gelesen; `pyproject.toml` Abhängigkeiten.
- **Übersprungen (mit Grund):** `crawl4ai/utils.py` (3.794 Zeilen, nur Signaturen nicht
  erhoben — Sammelmodul), `prompts.py` (LLM-Prompts, nicht fähigkeitsrelevant), `html2text/*`
  Implementierung (bekannter html2text-Fork; Übernahmekandidat, Detailprüfung bei Übernahme),
  `legacy/*` (abgekündigt), `docs/md_v2/*` Inhalte (nur Themenliste), `docs/examples/*`,
  Testinhalte (nur Namen als Existenzbeleg). **Kein Test wurde ausgeführt.**
- **Status-Vokabular je Fähigkeit:** `impl` (Code vorhanden) · `dok` (in docs/md_v2) ·
  `test` (Testdatei existiert; **nicht** ausgeführt) · `betrieb` (im Betrieb nachgewiesen —
  nur per Doc/HANDOVER [E]) · `stub`.

### 0.2 Evidenzlücken (blockieren die StealthBrowser-Spalte) [V]
| Baustein | Zugriff aus dieser Session | Folge |
|---|---|---|
| `noerix-dev/stealthbrowser` | **verweigert** (Gate `clone_dir_collision`: alter Redirect `noerix-admin/stealthbrowser` belegt den Checkout-Slot; nur eine **neue Session** kann anhängen) | StealthBrowser-Stand, Branches, offene PRs, `AGENTS.md`/`CLAUDE.md`/`docs/atlas/ATLAS.md` **nicht prüfbar** |
| `noerix-dev/webtrack`, `venarix`, `amarcustos_credentialmanager`, `skill_secugate`, `noerix_toolbox`, kanonischer Backlog | cross-tier, nicht angehängt | Spalte „anderer Baustein" nur aus Auftragstext [E] |
| noePort (Hub) | **erreichbar** | Volltext „astra"/„stealthbrowser-Plan": leer. Semantik: Juli-Entscheidung gefunden (→ §1). Projekt `stealthbrowser`: keine Notizen. |
| Juli-HANDOVER (`docs/stealthbrowser/HANDOVER.md`, 2026-07-06) | gelesen | einzige Quelle zum StealthBrowser-Ist-Stand → **[E]**, Stand ≥ 3 Monate alt |

**Im crawl4ai-Repo fehlende Dateien (ausdrücklich):** `AGENTS.md`, `CLAUDE.md`,
`docs/atlas/ATLAS.md`, `DECISIONS.md`, `AUDIT.md`. Vorhanden: `deploy/docker/ARCHITECTURE.md`.

---

## 1. Architekturentscheidung (Supersede-Spur)

- **Vorher (Hub, Juli 2026) [E]:** „Crawl4AI als Bibliothek, StealthBrowser als CDP-Engine;
  `enable_stealth`/`UndetectedAdapter` **nicht doppeln**." (Atome 5183/5184/5307/5188) und
  HANDOVER §3 („dockt an, launcht nicht").
- **Jetzt (Auftrag 2026-10-16) [E] — supersediert:** StealthBrowser wird **eigenständige**
  Basis; Crawl4AI-Fähigkeiten werden **übernommen/nachgebaut**, nicht per Laufzeit delegiert.
- **Was bleibt gültig [A]:** die „nicht doppeln"-Regel — jetzt als: *StealthBrowser hat genau
  **eine** Stealth-/Fingerprint-Schicht (seine eigene, E2); Crawl4AIs `enable_stealth`-Pfad wird
  **nicht** mitkopiert*, nur die Browser-Orchestrierung drumherum.
- **Scope-Grenze bleibt [E]:** kein Anti-Fraud-/2FA-Umgehen fremder Systeme; nur „nicht
  fälschlich als Bot geblockt werden" (HANDOVER §1; Threat-Report-Extract M5).

---

## 2. Fähigkeitsmatrix

Spalten: **Fähigkeit** | **Crawl4AI-Beleg (Datei · Commit · Status)** | **StealthBrowser-Stand** |
**anderer Baustein** | **Lücke** | **Vorgehen** (Ü=übernehmen · N=nachbauen · W=wiederverwenden ·
G=gesondert §3) | **Abhängigkeiten** | **Abnahmekriterium**

Kürzel in „StealthBrowser-Stand": **n.p.** = nicht prüfbar aus dieser Session [R];
**HO** = laut HANDOVER 2026-07-06 [E].

### A · Browserstart, CDP, Contexts, Profile, Sessions

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| A1 | Browser-Launch (chromium/firefox/webkit, headless, channel, extra_args, Flag-Builder) | `browser_manager.py` `ManagedBrowser.build_browser_flags/start` · 310b52b · impl/dok(`core/browser-crawler-config.md`)/test(`tests/browser/test_browser_manager.py`) | HO: eigener gehärteter Browser E0 ✅ betrieb | — | Flag-Builder/Multi-Engine [R] | W (eigener Launch) + N (Flag-Builder) | playwright **oder** patchright (eine Engine!) | Launch per Config reproduzierbar; Flags geloggt |
| A2 | CDP-Attach (`cdp_url`, `browser_mode="cdp"`, `_CDPConnectionCache`, `cdp_cleanup_on_close`) | `browser_manager.py` 585–654, 779 · 310b52b · impl/test(`test_cdp_strategy.py`, `test_cdp_cleanup_reuse.py`) | HO: SB **ist** der CDP-Endpoint (Gegenseite) | — | SB muss **beide Rollen** können: Engine *und* Orchestrator | N | — | Attach an fremden CDP + eigener Launch, Cleanup ohne Leak |
| A3 | Builtin/Standalone-Browser-Verwaltung (launch/status/kill; CLI `browser start/stop/view/restart`, `cdp`) | `browser_profiler.py` 960–1355 · 43738c9; `cli.py` 637–945 · 3f481e9 · impl/test(`test_builtin_browser.py`, `test_launch_standalone.py`) | n.p. | — | [R] | N (dünne CLI) | — | `sb browser start/status/stop` gegen eigenen Browser |
| A4 | Persistente Profile (`user_data_dir`, `use_persistent_context`, create/list/delete/**shrink**, interaktiver Login-Manager) | `browser_profiler.py` 134–817 · 43738c9; `browser_manager.py` 462–533 · impl/dok(`advanced/identity-based-crawling.md`)/test(`tests/profiler/*`, `test_profiles.py`, `test_profile_shrink.py`) | HO Aufgabe 3 „Session-Persistenz eigene Logins" **offen** | **amarcustos** (Credential-Zugang, [E]) | Profil-Lebenszyklus + Shrink | N; Login-Flow nur mit amarcustos-Grenze | — | Profil anlegen → Login manuell → Re-Use ohne Re-Login; Shrink reduziert Größe messbar |
| A5 | Context-Isolation (`create_isolated_context`, `browser_context_id`, `target_id`, `clone_runtime_state`) | `browser_manager.py` 533, 1141–1249 · impl/test(`test_browser_context_id.py`, `test_context_leak_fix.py`) | n.p. | — | [R] | N | — | Zwei Contexts teilen keine Cookies; kein Leak-Test rot |
| A6 | Sessions (`session_id`, `create_session`, `kill_session`, Page-Reuse) | `async_crawler_strategy.py` 147/412; `browser_manager.py` 1538–1753 · impl/dok(`advanced/session-management.md`)/test(`test_page_reuse_race_condition.py`) | n.p. | — | [R] | N | — | Mehrschritt-Crawl auf einer Session; Race-Test |
| A7 | Storage-State/Cookies/Header/UA (`storage_state`, `export_storage_state`, `cookies`, `headers`, `user_agent_mode=random`, Client-Hints) | `async_crawler_strategy.py` 1993; `user_agent_generator.py` · cba4a46 · impl/dok(`advanced/hooks-auth.md`) | n.p. | — | [R] | Ü (UA-Generator, Apache-2.0) + N | fake-useragent (optional) | Export/Import Storage-State round-trip; UA↔Client-Hints konsistent |
| A8 | Stealth-/Undetected-Adapter (`enable_stealth`, `PlaywrightAdapter/StealthAdapter/UndetectedAdapter`; Antibot-Heuristik + Fallback) | `browser_adapter.py` · 5568a9a; `antibot_detector.py` · 11b4576 · impl/dok(`advanced/anti-bot-and-fallback.md`, `undetected-browser.md`)/test(`tests/proxy/test_antibot_detector.py`) | HO: **E2 Fingerprint offen** | — | SB-Kernkompetenz | **N (eigen, E2)**; Adapter **nicht** kopieren (§1); Antibot-**Detektor** Ü | patchright *oder* camoufox | Fingerprint-Konsistenz-Test (UA↔JA3↔WebGL) grün; Detektor erkennt Block-Seiten in Fixtures |
| A9 | Proxy (`ProxyConfig`, RoundRobin-Rotation, Sticky-Sessions `proxy_session_id/ttl`, `from_env`) | `proxy_strategy.py` · f6f7f1b · impl/dok(`advanced/proxy-security.md`)/test(`tests/proxy/*` 8 Dateien) | HO: E1 Residential-Egress ✅ betrieb; Rotation/Sticky n.p. | — | Rotation/Sticky [R] | Ü | — | Sticky-Session hält Proxy über N Requests; Rotation deterministisch testbar |
| A10 | Geolocation/Locale/Timezone | `async_configs.py` `GeolocationConfig` 493; `CrawlerRunConfig.locale/timezone_id` 1737–1739 · impl/dok | n.p. | — | [R] | N | — | Seite sieht gesetzte Locale/TZ/Geo |
| A11 | Lifecycle-Hooks (9: `on_browser_created`…`before_return_html`) | `async_crawler_strategy.py` 102–110, 164–210 · 60886d1 · impl/dok(`advanced/hooks-auth.md`)/test(`tests/docker/test_hooks_*`) | n.p. | — | [R] | N (gleiche Namen → Kompatibilität) | — | Jeder Hook feuert genau einmal pro Phase (Test) |

### B · Navigation, JavaScript, Wartebedingungen, dynamische Inhalte

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| B1 | Warten: `wait_until`, `page_timeout`, `wait_for` (`css:`/`js:`), `smart_wait`, `csp_compliant_wait`, `wait_for_images`, `delay_before_return_html` | `async_crawler_strategy.py` 234–344; Config 1757–1765 · impl/dok(`core/page-interaction.md`) | n.p. | — | [R] | Ü (Wait-Logik) | — | Fixture „spät geladener Inhalt" wird vollständig erfasst; Timeout-Fall sauber |
| B2 | JS-Ausführung `js_code`, `js_only`, `execute_user_script`/`robust_…`, `js_execution_result` | 2018–2168; Route `/execute_js` · impl/test(docker) | n.p. | — | [R] | Ü | — | Rückgabewerte + Fehler je Snippet strukturiert |
| B3 | Scroll/Lazy-Load: `scan_full_page`, `scroll_delay`, `max_scroll_steps`, `safe_scroll`, `csp_scroll_to`, **VirtualScrollConfig** | 2293–2386; `async_configs.py` 1110 · impl/dok(`advanced/virtual-scroll.md`, `lazy-loading.md`)/test(`tests/test_virtual_scroll.py`) | n.p. | — | [R] | Ü | — | Virtual-Scroll-Fixture liefert alle Items |
| B4 | iFrames einbetten, Shadow-DOM flatten | 344; Config 1776–1777 · impl | n.p. | — | [R] | Ü | — | iFrame-/Shadow-Fixture im HTML sichtbar |
| B5 | Overlay-/Consent-Entfernung, `simulate_user`, `override_navigator`, `magic` | 1510–1545; Config 1778–1782 · impl | n.p. | — | [R] | Ü (Overlay/Consent); `override_navigator` **nicht** (kollidiert mit E2) | — | Consent-Fixture: Inhalt ohne Overlay |
| B6 | C4A-Script DSL (compile/validate/generate) | `script/c4a_compile.py` · 08a2cda; `c4ai_script.py` · ca03acb · impl/dok(`core/c4a-script.md`) | n.p. | — | [R] | G (optional, später) | lark | — |
| B7 | Redirect-/Status-Tracking (`redirected_url`, `redirected_status_code`, `response_headers`, `status_code`) | `models.py` CrawlResult · 55956a8 · impl/test(`test_pr_1435_…`, `test_raw_html_redirected_url.py`) | n.p. | **webtrack** braucht das (Provenienz) | [R] | N (Pflicht für §4) | — | Redirect-Fixture: Kette + Endstatus im Ergebnis |
| B8 | `check_robots_txt` | Config 1830 · impl | n.p. | — | [R] | N | — | Disallow-Fixture wird respektiert (konfigurierbar) |
| B9 | Eingaben `raw:`-HTML und `file://` | dok(`core/local-files.md`)/test(`tests/test_raw_html_*`) | n.p. | — | [R] | Ü | — | Gleiche Pipeline für URL/raw/file |

### C · Crawling, Link-Ermittlung, URL-Filter, Traversierung, Abbruch

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| C1 | Deep-Crawl BFS/DFS/Best-First (`max_depth`, `max_pages`, `include_external`, `score_threshold`) | `deep_crawling/{bfs,dfs,bff}_strategy.py` · 43738c9/dc5e2a8 · impl/dok(`core/deep-crawling.md`)/test(`tests/deep_crawling/*`, `tests/regression/test_reg_deep_crawl.py`) | n.p. | **venarix** (Quellenadapter/Taktung [E]) — Grenze klären | [R] | Ü | — | Fixture-Site (3 Ebenen): Seitenmenge/Reihenfolge je Strategie deterministisch; `max_pages`-Grenze hält (#859) |
| C2 | URL-Filter-Kette: Pattern/ContentType/Domain/ContentRelevance/SEO | `deep_crawling/filters.py` · 11b4576 · impl/dok | n.p. | venarix „Vorfilter" [E] | [R] | Ü | — | Filter-Statistik stimmt mit Fixture |
| C3 | URL-Scorer: Keyword/PathDepth/ContentType/Freshness/DomainAuthority/Composite | `deep_crawling/scorers.py` · dad592c · impl | n.p. | **noespex** (Relevanz) — nur Mechanik hier | [R] | Ü (Mechanik) | — | Score-Reihenfolge reproduzierbar |
| C4 | Deep-Crawl Abbruch + **Wiederaufnahme** | test(`test_deep_crawl_cancellation.py`, `test_deep_crawl_resume*.py`) · impl | n.p. | — | [R] | Ü | — | Abbruch mittendrin → Resume setzt ohne Doppelabruf fort |
| C5 | URL-Seeding (Sitemap/CommonCrawl, `live_check`, `extract_head`, Pattern, `hits_per_sec`, BM25) | `async_url_seeder.py` · 7c0cc3e; `SeedingConfig` 2339 · impl/dok(`core/url-seeding.md`) | n.p. | venarix Quellenadapter [E] | [R] | Ü (Sitemap) · G (CommonCrawl = externer Index) | — | Sitemap-Fixture → URL-Liste + Head-Daten |
| C6 | Domain-Mapping (8 Quellen, `Soft404Fingerprint`) | `domain_mapper.py` · 858c827 · impl/dok(`core/domain-mapping.md`)/test(`test_reg_domain_mapper.py`) | n.p. | venarix [E] | [R] | Ü (lokal mögliche Quellen) · G (externe Indizes) | — | Soft-404 erkannt in Fixture |
| C7 | Link-Extraktion/-Scoring/-Preview (intern/extern, `exclude_*`, `score_links`, `LinkPreview` Head-Extraktion) | `link_preview.py` · 5b815c2; `LinkPreviewConfig` 1155 · impl/dok(`core/link-media.md`)/test(`test_merge_head_data_scoring.py`) | n.p. | — | [R] | Ü | — | Links klassifiziert + Head-Metadaten bei N Links |
| C8 | Adaptive Crawling (Statistical/Embedding, Confidence, Stop, KB-Export) | `adaptive_crawler.py` · 3a75dd3 · impl/dok(`core/adaptive-crawling.md`)/test(`tests/adaptive/*`) | n.p. | **noespex** (adaptive Tracking-Steuerung = dessen Hoheit [E]) | Grenzfrage | **G** (§3.4) | numpy, alphashape, shapely; Embedding-Variante: torch/transformers | — |
| C9 | Multi-Config per `url_matcher`/`match_mode` | Config 1894–1895 · impl/test(`tests/test_multi_config.py`) | n.p. | — | [R] | Ü | — | URL-Muster wählt Config |

### D · Parallelität, Ressourcen, Taktung, Timeouts, Wiederaufnahme

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| D1 | `arun_many` + Dispatcher (`MemoryAdaptiveDispatcher`, `SemaphoreDispatcher`), Streaming | `async_dispatcher.py` · 6ea0e38 · impl/dok(`advanced/multi-url-crawling.md`, `crawl-dispatcher.md`)/test(`tests/memory/test_dispatcher_stress.py`) | n.p. | venarix „Taktung" [E] | [R] | Ü | psutil | N URLs parallel, Speicher-Drossel greift (Test mit Limit) |
| D2 | Rate-Limiter je Domain (Basisverzögerung, Backoff bei 429/503, `max_retries`) | `async_dispatcher.py` 28–89 · impl | n.p. | venarix Taktung [E] — Grenze | [R] | Ü | — | 429-Fixture → Backoff messbar |
| D3 | Crawler-Monitor (Terminal-UI) | `components/crawler_monitor.py` · 1029815 · impl/test | n.p. | — | [R] | G (optional) | rich | — |
| D4 | Ressourcensteuerung `memory_saving_mode`, `max_pages_before_recycle`, `text_mode`, `light_mode` | `BrowserConfig` 886–898 · impl | n.p. | — | [R] | N | — | Recycle nach N Seiten nachweisbar |
| D5 | Timeouts/Retry/Fallback (`page_timeout`, `wait_for_timeout`, `max_retries`, `fallback_fetch_function`) | Config 1758–1760, 1901–1902 · impl | n.p. | — | [R] | Ü | — | Timeout-Fixture: Ergebnis `success=False` + Fehlertext, Fallback greift |
| D6 | Wiederaufnahme (Deep-Crawl-Resume; `CrawlState.save/load`; Server-Jobs) | s. C4/C8; `deploy/docker/job.py` · 60886d1 | n.p. | — | [R] | Ü (C4) · G (Server-Jobs/Redis) | — | s. C4 |
| D7 | Browser-Pool 3-Tier + Janitor + Speicher-Governor (Server) | `deploy/docker/crawler_pool.py` · f6ab207; `governor.py` · 60886d1; `ARCHITECTURE.md` | n.p. | — | [R] | G (nur wenn SB-HTTP-Dienst) | redis (optional) | — |

### E · HTML-Bereinigung, Markdown, strukturierte Extraktion, Tabellen, Metadaten

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| E1 | Scraping-Strategien (LXML/BS4): `css_selector`, `target_elements`, `excluded_tags/selector`, `keep_attrs`, `remove_forms`, `word_count_threshold`, Bild-Scoring/srcset, Metadaten | `content_scraping_strategy.py` · a45c678 · impl/dok(`core/content-selection.md`)/test(`tests/test_scraping_strategy.py`, `tests/regression/test_reg_content.py`) | HO: „bereinigt Boilerplate" ✅ betrieb (Umfang n.p.) | — | [R] | Ü | lxml, beautifulsoup4, cssselect | Fixture-HTML → identisches `cleaned_html` wie Crawl4AI (Diff dokumentiert) |
| E2 | Markdown (`DefaultMarkdownGenerator`, html2text-Fork, Zitate/Referenzen, `fit_markdown`) | `markdown_generation_strategy.py` · 669b466; `html2text/` · dba38c7 · impl/dok(`core/markdown-generation.md`, `fit-markdown.md`)/test(`test_markdown_generator_validation_1880.py`) | HO: „liefert sauberes Markdown" ✅ betrieb (Umfang n.p.) | — | [R] | **Ü** html2text-Fork (Apache-2.0 → NOTICE) | — | Gleiche Fixtures → Markdown-Diff ≤ dokumentierte Abweichungen |
| E3 | Strukturierte Extraktion ohne LLM: JsonCss/JsonXPath/JsonLxml/Regex (+ Pattern-/Schema-Generator) | `extraction_strategy.py` 1043–2827 · ec560f1 · impl/dok(`extraction/no-llm-strategies.md`)/test(`test_reg_extraction.py`) | n.p. | — | [R] | Ü | lxml | Schema-Fixture → identische JSON-Ausgabe |
| E4 | Tabellen (Default/LLM/No; GFM-konform) | `table_extraction.py` · a87e8c1 · impl/dok(`core/table_extraction.md`)/test(`test_table_gfm_compliance.py`) | n.p. | — | [R] | Ü (Default) · G (LLM-Variante) | — | Tabellen-Fixture → GFM-Tabelle |
| E5 | Metadaten/Medien (`metadata`, `media` Bilder/Video/Audio, `head_fingerprint`) | `models.py`; `content_scraping_strategy.py`; `cache_validator.py` · impl | n.p. | — | [R] | Ü | — | OG/Title/Description im Ergebnis |
| E6 | SSL-Zertifikat erfassen | `ssl_certificate.py` · 230f22d · impl/dok(`advanced/ssl-certificate.md`)/test(`test_pyopenssl_*`) | n.p. | — | [R] | Ü | pyOpenSSL ≥ 25.3 | Zertifikat-Felder bei HTTPS-Fixture |

### F · Chunking, Inhaltsfilter, optionale LLM-Extraktion

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| F1 | Chunking: Regex/NlpSentence/TopicSegmentation/FixedLengthWord/SlidingWindow/OverlappingWindow | `chunking_strategy.py` · c837c0d · impl/dok(`extraction/chunking.md`) | HO: Embeddings-Pipeline existiert (Chunking n.p.) | noeport (Staging) — Grenze | [R] | Ü (ohne NLTK-Varianten optional) | nltk (optional) | Chunk-Grenzen reproduzierbar |
| F2 | Inhaltsfilter: BM25 / Pruning / LLM | `content_filter_strategy.py` · 57b0d09 · impl/dok(`core/fit-markdown.md`) | n.p. | — | [R] | Ü (BM25, Pruning) · G (LLM) | rank-bm25, snowballstemmer | `fit_markdown` auf Fixture entspricht Crawl4AI |
| F3 | LLM-Extraktion (`LLMExtractionStrategy`, `LLMConfig` Provider/Backoff/Token-Usage) | `extraction_strategy.py` 533–1020; `async_configs.py` 2216 · impl/dok(`extraction/llm-strategies.md`) | n.p. | — | [R] | **G** (§3.1: nur mit steckbarem Provider inkl. lokal) | unclecode-litellm (Fork!) | — |
| F4 | Cosine/Embedding-Clustering | `extraction_strategy.py` 172–515 · impl | n.p. | — | [R] | G (schwere Extras) | torch, transformers | — |
| F5 | LLM-gestützte Schema-/Pattern-Generierung | 1692/1764/2744 · impl | n.p. | — | [R] | G | wie F3 | — |
| F6 | `/ask` + llms.txt-Kontext (Crawl4AI-eigener Doku-Assistent) | `server.py` 1006; dok(`core/ask-ai.md`, `llmtxt.md`) | — | — | nicht Ziel | G (entfällt) | — | — |

### G · Archive, Screenshots, Ausgabeformate

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| G1 | Screenshot (Full-Page-Scroller, naiv, aus PDF, `screenshot_wait_for`, Höhen-Schwelle, `force_viewport`) | `async_crawler_strategy.py` 1777–1959; Route `/screenshot` · impl/test(`test_pr_1463_device_scale_factor.py`) | n.p. | webtrack (Snapshots [E]) — Grenze: SB liefert, webtrack verwaltet | [R] | Ü | pillow | Fixture → PNG; lange Seite vollständig |
| G2 | PDF-Export der Seite | 1589; Route `/pdf` · impl | n.p. | — | [R] | Ü | — | PDF-Bytes, Seitenzahl > 0 |
| G3 | MHTML-Capture | 1602; Config `capture_mhtml` · impl | n.p. | webtrack (Inhaltsbeleg für Vorher/Nachher [E]) | **Pflicht für §4** | Ü | — | MHTML offline wieder renderbar |
| G4 | PDF-**Eingabe** verarbeiten (`NaivePDFProcessorStrategy`, `PDFContentScrapingStrategy`) | `processors/pdf/*` · a87e8c1/3a75dd3 · impl/dok(`advanced/pdf-parsing.md`) | n.p. | — | [R] | Ü | pypdf (extra) | PDF-Fixture → Text + Metadaten |
| G5 | Ergebnisformate: `CrawlResult` (JSON-Dump), Markdown-Varianten (raw/fit/with_citations/references), `cleaned_html`, `fit_html`; CLI `-o all/json/md/md-fit` | `models.py` · 55956a8; `cli.py` 1023 · impl/dok(`core/crawler-result.md`) | n.p. | — | [R] | N (eigenes Ergebnismodell, Feldnamen kompatibel) | pydantic | Ergebnis-Schema versioniert + dokumentiert |
| G6 | Server-seitiger Artefakt-Speicher (`/artifacts/{id}`, pfadsicher, quotiert) | `deploy/docker/artifacts.py` · 60886d1 · impl | n.p. | — | [R] | Ü (wenn SB-HTTP) | — | Kein Caller-Pfad; Quota greift |

### H · Netzwerk-/Konsolenbeobachtung, Downloads, Fehlerdiagnose

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| H1 | `capture_network_requests`, `capture_console_messages`, `log_console` | `async_crawler_strategy.py` 619–679, 1679 · impl/dok(`advanced/network-console-capture.md`) | n.p. | — | [R] | Ü | — | Fixture mit XHR + console.error → beides im Ergebnis |
| H2 | Downloads (`accept_downloads`, `downloads_path`, `downloaded_files`) | `BrowserConfig` 876–877 · impl/dok(`advanced/file-downloading.md`) | n.p. | — | [R] | Ü | — | Download-Fixture → Datei + Pfad im Ergebnis |
| H3 | Ressourcen-Blocking (`text_mode`, `avoid_ads/avoid_css`, deklarativ `block_resources`) | `BrowserConfig`; `deploy/docker/hook_registry.py` · 60886d1 · impl/test(`test_resource_filtering.py`) | n.p. | — | [R] | Ü | — | Bilder geblockt → kein Bild-Request im Netzlog |
| H4 | Logging (`AsyncLogger/AsyncFileLogger`, `url_status`, `error_status`), `error_message`, `crawl_stats` | `async_logger.py` · c06a3dc; `models.py` · impl | n.p. | — | [R] | N (eigener Logger, gleiche Ereignisse) | — | Jeder Abruf hat Status+Dauer im Log |
| H5 | Antibot-Erkennung + Fallback-Diagnose | s. A8 (`antibot_detector.py`) | HO: — | — | [R] | Ü | — | Block-Fixtures (403/Challenge-HTML) klassifiziert |
| H6 | Server-Monitoring (health, prometheus, `/monitor/*`, WS-Dashboard) | `deploy/docker/monitor*.py`; `ARCHITECTURE.md` | n.p. | — | [R] | G (nur SB-HTTP) | — | — |

### I · Cache, Persistenz, Aktualitätsprüfung, Ergebnisprovenienz

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| I1 | `CacheMode` (ENABLED/DISABLED/READ_ONLY/WRITE_ONLY/BYPASS), `CacheContext` | `cache_context.py` · 8ec12d7 · impl/dok(`core/cache-modes.md`) | n.p. | **webtrack** (Snapshots/Historie [E]) — **zuerst prüfen** | [R] | Ü (Modi) | — | Jeder Modus per Test (read/write-Flags) |
| I2 | SQLite-Cache (`AsyncDatabaseManager`, `acache_url/aget_cached_url`, Metadaten, Migrationen) | `async_database.py` · f6f7f1b; `migrations.py` · impl | n.p. | webtrack [E] | [R] | **W webtrack** wenn deckungsgleich, sonst Ü | aiosqlite | Cache-Hit/Miss reproduzierbar; **kein** zweiter kanonischer Speicher (§4) |
| I3 | Aktualitätsprüfung ohne Browser (ETag/Last-Modified + `head_fingerprint`, `check_cache_freshness`, `cached_at`, `cache_status`) | `cache_validator.py` · f6f7f1b · impl/test(`tests/cache_validation/*`) | n.p. | webtrack „Diffs/Änderungshistorie" [E] | [R] | Ü (leichtgewichtig, httpx) | httpx | Unveränderte Seite → kein Browser-Abruf |
| I4 | **Ergebnisprovenienz** — vorhanden: `url`, `redirected_url`, `status_code`, `response_headers`, `head_fingerprint`, `cached_at`, `cache_status`, `ssl_certificate`, `crawl_stats`, `session_id`, `network_requests`; Config-Vertrauen `Provenance`/`UntrustedConfigError` | `models.py` CrawlResult; `async_configs.py` 166–176 · 60886d1 · impl | n.p. | webtrack [E] | **Crawl4AI deckt §4 nicht ab:** keine **stabile Quellenkennung**, nur `cached_at` statt Abrufzeit, kein Versionsbezug, kein Inhaltsbeleg-Pointer | **N (Pflicht)** — Provenienz-Block im SB-Ergebnis: `source_id`, `fetched_at`, `version_ref` (ETag/Last-Modified/Hash), `evidence` (MHTML/HTML-Archiv-Ref), `redirect_chain` | — | Vorher/Nachher aus zwei Ergebnissen **rekonstruierbar** ohne Re-Fetch (webtrack-Abnahme) |
| I5 | Server-Jobs (Redis-Resultate, `/crawl/job`, `/llm/job`, Webhook mit Pinning) | `deploy/docker/job.py`, `webhook.py` · 60886d1 · test(`test_security_webhook_pinning.py`) | n.p. | Hermes (Orchestrierung [E]) | [R] | G (Orchestrierung ist Hermes) | redis | — |

### J · CLI, Python-API, HTTP-/Service-Schnittstellen, Integrationswege

| # | Fähigkeit | Crawl4AI-Beleg | SB-Stand | anderer Baustein | Lücke | Vorgehen | Abhängigk. | Abnahme |
|---|---|---|---|---|---|---|---|---|
| J1 | Python-API `AsyncWebCrawler.arun/arun_many/aseed_urls/amap_domain/aprocess_html` | `async_webcrawler.py` · 7059170 · impl/dok(`complete-sdk-reference.md`) | HO: `kb_search`/`kb_ingest` Tooling | — | [R] | N (gemeinsame Kernlogik; Namen kompatibel) | — | Beispiel aus Crawl4AI-Quickstart läuft mit SB-Import 1:1 |
| J2 | CLI `crwl crawl/browser/cdp/profiles/config/examples` (`-q`, `--deep-crawl`, `-o`) | `cli.py` · 3f481e9 · impl/dok(`core/cli.md`) | n.p. | — | [R] | N (dünn über Kern) | click | `sb crawl URL -o md` identisch zu API |
| J3 | HTTP-Dienst: `/crawl`, `/crawl/stream`, `/md`, `/html`, `/screenshot`, `/pdf`, `/execute_js`, `/llm`, `/config/dump`, `/token`, `/crawl/job`, `/artifacts`, `/schema`, `/hooks/info`, `/health`, `/metrics` | `deploy/docker/server.py`, `job.py` · 60886d1 · impl/dok(`core/self-hosting.md`)/test(`tests/docker/*` 15) | HO: `kb-mcp` FastMCP `:8000/mcp` ✅ betrieb | — | [R] | N (dünn über Kern) | fastapi/uvicorn | Gleiche Antwort wie API bei gleicher Eingabe |
| J4 | **Sicherheitshärtung 0.9.0:** `AuthGateMiddleware` (fail-closed), `EgressBroker`+`PinningProxy` (SSRF/DNS-Rebinding), `BodySizeLimit`-Governor, deklarative `hook_registry` (kein `exec`), `llm_broker`-Allowlist, pfadsichere Artefakte, Webhook-Pinning | `deploy/docker/{auth_gate,egress_broker,egress_proxy,governor,hook_registry,llm_broker,artifacts}.py` · 60886d1; `SECURITY-VERIFY.md`; `tests/run_security_tests.py` | n.p. | **quevrix/SecuGate** (Egress-/Scope-Guard-Idee) | [R] | **Ü** (Muster + Code; Apache-2.0) | — | Security-Tests aus `deploy/docker/tests` gegen SB-HTTP grün |
| J5 | MCP-Bridge (Tools `md/html/screenshot/pdf/execute_js/crawl/ask`; SSE+WS) | `deploy/docker/mcp_bridge.py` · 60886d1 · test(`tests/mcp/*`) | HO: `search_knowledge` MCP ✅ betrieb | — | [R] | N (Tools auf Kern mappen) | mcp | Tool-Liste per Schema abrufbar |
| J6 | Docker-Client-SDK | `docker_client.py` · a87e8c1 · test | n.p. | — | [R] | G (optional) | — | — |
| J7 | `CrawlerHub`-Registry + Beispiel-Crawler | `hub.py` · c6d4808; `crawlers/` | n.p. | venarix Quellenadapter [E] | [R] | G (Adapter-Hoheit venarix) | — | — |
| J8 | Cloud-CLI | `cloud/cli.py` · 43738c9 | — | — | externer Dienst | G (entfällt) | — | — |
| J9 | Playground-/Monitor-UI | `deploy/docker/static/*` | n.p. | — | — | G (optional) | — | — |
| J10 | Config-(De)Serialisierung `to_dict/from_dict/dump/load` + Vertrauensstufe | `async_configs.py` · 60886d1 · test(`test_serialization.py`, `test_config_object.py`) | n.p. | — | [R] | Ü | pydantic | Round-trip-Test; untrusted Config abgelehnt |
| J11 | **HTTP-only-Crawler** (ohne Browser: `AsyncHTTPCrawlerStrategy`, `HTTPCrawlerConfig`) | `async_crawler_strategy.py` 2466–2828; `async_configs.py` 1250 · impl | n.p. | — | [R] | Ü | aiohttp/httpx | Statische Fixture ohne Browser identisch extrahiert |
| J12 | Install/Setup (`crawl4ai-setup`, Modell-Loader) | `install.py`, `model_loader.py` | n.p. | — | [R] | N (eigenes Setup) | — | Frische Umgebung → Setup ohne Netz außer Browser-Binary |

---

## 3. Gesondert aufgeführt (optional / extern / nicht sinnvoll lokal)

1. **LLM-gestützte Fähigkeiten (F3–F5, E4-LLM, F2-LLM):** erlaubt nur mit **steckbarem
   Provider inkl. lokalem Modell** (Auftrag: „ohne verpflichtenden externen Dienst").
   Crawl4AI bindet `unclecode-litellm==1.81.13` (**Fork**, Pin) — Übernahme dieses Forks ist
   eine zu begründende Abhängigkeit; Alternative: schmale eigene Provider-Schnittstelle.
2. **Externe Indizes:** CommonCrawl-Seeding (C5), externe Domain-Quellen (C6), Cloud-CLI (J8),
   `/ask`-Doku-Assistent (F6) — **nicht Ziel**; nur lokal belegbare Quellen übernehmen.
3. **Schwere Extras:** Cosine/Embedding (F4, C8-Embedding) mit torch/transformers — optional,
   getrennt installierbar, nicht im Kern.
4. **Grenzfragen zu Nachbarmodulen (Entscheidung nötig, nicht hier):**
   - **C8 Adaptive Crawling** → Relevanz/adaptives Tracking ist **noespex**; in SB höchstens die
     Mechanik (Zustand, Link-Ranking-API) als Bibliothek. *Empfehlung [A]: nicht übernehmen.*
   - **C1/C2/C5/C6/D1/D2** ↔ **venarix** (Quellenadapter, Taktung, Vorfilter): SB liefert
     Traversierung+Taktung als **Mechanik**, venarix entscheidet **was/wann**.
   - **I1–I4** ↔ **webtrack**: Cache ≠ Snapshot-Historie; SB-Cache bleibt Abruf-Cache, kein
     zweiter Wissensspeicher (§4). **WebTrack zuerst prüfen**, bevor I2 gebaut wird.
   - **D7/H6/I5/J3-Jobs** ↔ **Hermes**: Orchestrierung/Jobs nicht in SB; SB-HTTP bleibt dünn.
5. **Nicht übernehmen (Kollision mit SB-Kern):** `enable_stealth`/`UndetectedAdapter`/
   `override_navigator` (A8/B5) — SB hat **eine** eigene Stealth-Schicht (E2). Nur der
   **Antibot-Detektor** (Erkennen, nicht Tarnen) wird übernommen.

---

## 4. Vorgeschlagene Umsetzungsreihenfolge (klein, prüfbar) [A]

Voraussetzung **Schritt 0** (StealthBrowser-Session): `AUDIT.md` mit Coverage-Manifest,
Branches/PRs prüfen, **StealthBrowser-Spalte dieser Matrix füllen**, `AGENTS.md`/`CLAUDE.md`/
`ATLAS.md` prüfen (fehlende benennen), WebTrack/venarix/amarcustos/secugate-Schnittstellen
einsehen. Erst dann:

| Schritt | Inhalt | Matrix | Warum zuerst |
|---|---|---|---|
| 1 | **Ergebnismodell + Provenienz-Block** (`source_id`, `fetched_at`, `version_ref`, `evidence`, `redirect_chain`) | G5, I4, B7 | Fundament; §4-Pflicht; webtrack-Abnahme |
| 2 | Fetch-Kern: Launch/Attach wiederverwenden, Warten, JS, Status/Redirect/Header | A1/A2, B1, B2, B7 | alles weitere hängt daran |
| 3 | HTML → `cleaned_html` → Markdown (html2text-Fork + LXML-Scraping) + NOTICE | E1, E2, E5 | Kernnutzen; Fixture-Vergleich mit Crawl4AI |
| 4 | Links + URL-Filter | C7, C2 | Basis für Traversierung |
| 5 | Cache-Modi + Aktualitätsprüfung (**nach WebTrack-Prüfung**) | I1, I3, (I2) | spart Browser-Abrufe |
| 6 | Parallelität + Rate-Limiter + Timeouts/Retry | D1, D2, D5 | Betriebsfähigkeit |
| 7 | Deep-Crawl BFS/DFS/Best-First + Scorer + Abbruch/Resume | C1, C3, C4, C9 | Traversierung |
| 8 | Captures: Screenshot/PDF/**MHTML**, Netz/Konsole, Downloads, Ressourcen-Blocking | G1–G3, H1–H3 | Inhaltsbelege für webtrack |
| 9 | Strukturierte Extraktion ohne LLM, Tabellen, Chunking, BM25/Pruning-Filter | E3, E4, F1, F2 | Extraktion |
| 10 | Sitemap-Seeding, Domain-Mapping (lokale Quellen) | C5, C6 | Entdeckung |
| 11 | Dünne CLI + dünner HTTP-Dienst **mit 0.9.0-Härtungsmuster**, MCP-Tools | J2–J5, J10, J11 | Zugänge |
| 12 | Sessions/Profile/Storage-State für **eigene** Logins (amarcustos-Grenze; LastPass bleibt) | A4–A7, A11 | Login-Persistenz |
| 13 | Optional: LLM-Schicht mit steckbarem Provider (lokal möglich) | F3–F5 | zuletzt, optional |

**Abnahme-Prinzip für jeden Schritt:** lokales Fixture-Set (statische Seiten: Redirect-Kette,
spät geladener Inhalt, Virtual-Scroll, Tabelle, Consent-Overlay, iFrame/Shadow-DOM, 403/Challenge,
Timeout, Download, PDF) wird von **Crawl4AI 0.9.0 und StealthBrowser identisch** verarbeitet;
Abweichungen im Ergebnis werden **begründet dokumentiert**. Tests je Fähigkeit; Repository-Checks
vor jedem Push; eigener Branch + Draft-PR; kein Merge; kein Deployment.

---

## 5. Abhängigkeiten von Crawl4AI 0.9.0 (für begründete Übernahme) [V]

Kern (`pyproject.toml`): aiofiles, aiohttp, aiosqlite, anyio, lxml, **unclecode-litellm (Fork, Pin)**,
numpy, pillow, **playwright + patchright (beide!)**, python-dotenv, requests, beautifulsoup4,
**playwright-stealth**, xxhash, rank-bm25, snowballstemmer, pydantic, pyOpenSSL, psutil, PyYAML,
nltk, rich, cssselect, httpx[http2], fake-useragent, click, chardet, brotli, humanize, lark,
alphashape, shapely. Extras: `pdf` (pypdf), `torch`, `transformer`, `cosine`, `sync` (selenium).
**Für SB zu entscheiden:** *eine* Browser-Engine (playwright **oder** patchright/camoufox), kein
playwright-stealth (Kollision E2), litellm-Fork nur bei F3, alphashape/shapely nur bei C8 (entfällt).

---

## 6. Festhalteblock (für `/judge` / DECISIONS — noch nicht persistiert)

- **D-SB-01 [E→A]:** Supersede der Juli-Entscheidung „Crawl4AI als Bibliothek + SB als
  CDP-Engine" durch „SB eigenständig, Crawl4AI-Fähigkeiten übernehmen/nachbauen"
  (Auftrag 2026-10-16). „Nicht doppeln" bleibt als Ein-Stealth-Schicht-Regel.
- **D-SB-02 [A]:** Crawl4AI deckt die §4-Provenienz-Anforderung **nicht** ab → eigener
  Provenienz-Block ist Schritt 1.
- **D-SB-03 [A]:** Adaptive Crawling (C8) nicht übernehmen (noespex-Hoheit); venarix/webtrack/
  Hermes-Grenzen wie §3.4.
- **Evidenzlücke E-SB-01 [V]:** StealthBrowser-Repo und Nachbar-Repos aus dieser Session
  unerreichbar; Matrix-Spalte „StealthBrowser-Stand" offen; „astra"-Plan nicht auffindbar
  (Repo, Hub, Docs).

---

## 7. Übergabe an die StealthBrowser-Session

1. Diese Datei lesen; §0.2 Evidenzlücken schließen (Repo, Branches, PRs, Nachbarmodule).
2. Spalte „StealthBrowser-Stand" je Zeile mit `impl/dok/stub/test/betrieb` + Dateibeleg füllen.
3. Schritt 0 (`AUDIT.md`) → dann §4 Schritt 1 beginnen; Draft-PR; Bericht nach Auftrag §6.
