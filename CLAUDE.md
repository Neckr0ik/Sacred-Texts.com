# CLAUDE.md · Neckr0ik/sacred-texts.com

Copia del proyecto **mahabrain** (origen: `jonnyg23/mahabrain`): un scrape de sacred-texts.com convertido en un vault de
Obsidian. Casi todo el repo (~2 GB) es contenido estático en `sacred_obsidian_vault/` (archivos `.md`, 120k+ según el README,
scrapeado en marzo de 2023, edición ISTA 9.0). El código es pequeño: un spider de Scrapy y un script de formateo.

## Estructura
- `sacred_obsidian_vault/`: el vault (carpetas por sección del sitio: `bib`, `bud`, `afr`, ...; `.md` sueltos en la raíz).
  **No lo leas ni lo recorras entero**: es enorme. Muestrea solo lo necesario.
- `sacred_texts_scraper/`: proyecto Scrapy (`settings.py` con `ROBOTSTXT_OBEY = True`; `spiders/books_spider.py`, spider
  `books`, que guarda el `<BODY>` HTML de cada página como `.md` en `./sacred_obsidian_vault`).
- `format_vault_files.py`: utilidades sobre el vault (`html2md` con markdownify, `replace_newlines`,
  `find_control_unicode_chars`, `replace_unicode`) aplicadas con `apply_func_to_files`.
- `scrapy.cfg`, `requirements.txt` (versiones fijadas: Scrapy 2.7.1, markdownify, beautifulsoup4, black...).
- `ISTA_OV.png`, `LICENSE`, `.gitattributes` (todo marcado como `linguist-vendored`, lenguaje Python).

## Comandos (del README)
```bash
pip install -r requirements.txt   # en venv/pyenv, Python 3.7+
scrapy crawl books                 # NO ejecutar salvo necesidad real (ver abajo)
```
No hay tests ni CI.

## Gotchas
- **No ejecutes el scraper**: el README advierte que puede considerarse abuso de sacred-texts.com. Usa el vault ya incluido.
- `format_vault_files.py` usa `vault_location = "../sacred_obsidian_vault/"` (ruta relativa al directorio padre) y, por
  defecto, solo busca caracteres de control sin escribir; la parte que reescribe archivos (`write=True`) está comentada.
  Revísalo antes de ejecutarlo, porque modifica miles de archivos en sitio.
- Clon superficial y repo pesado (~600 MB de `.git`): evita `git log -p`, `git grep` o diffs sobre el vault.
- El `.gitignore` ignora `*.json`, `*.csv` y patrones `*password*`/`*creds*`.
- Los enlaces del README apuntan al repo original `jonnyg23/mahabrain`.

## Memoria persistente (Graphiti MCP)
Este proyecto usa el connector **Graphiti** (memoria en grafo compartida entre sesiones).
- `group_id` de este repo: `sacred-texts-com`. Datos del usuario y transversales: `neckr0ik-global`.
- Al empezar una tarea: busca contexto con `search_memory_facts` y `search_nodes` usando `group_ids: ["sacred-texts-com", "neckr0ik-global"]`.
- Al terminar algo relevante (decisión de arquitectura, convención nueva, bug resuelto y su causa, preferencia del usuario, estado de un despliegue): guárdalo con `add_memory` (`group_id: "sacred-texts-com"`, `source: "text"`) con un nombre descriptivo.
- Nunca guardes secretos, API keys, tokens, contraseñas ni datos personales sensibles.
- Si Graphiti no está disponible en la sesión, continúa normalmente.
