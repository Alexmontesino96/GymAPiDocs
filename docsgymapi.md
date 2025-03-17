├── .dockerignore
├── .github
    └── workflows
    │   └── ci.yml
├── .gitignore
├── .pre-commit-config.yaml
├── Dockerfile
├── LICENSE
├── Makefile
├── Procfile
├── README.md
├── alembic.ini
├── alembic
    ├── README
    ├── env.py
    ├── script.py.mako
    └── versions
    │   ├── 0001_create_models.py
    │   ├── 0002_create_admin_user_model.py
    │   ├── 0003_increase_user_image_text_length.py
    │   ├── 0004_add_settings_to_user_model.py
    │   ├── 0005_add_type_and_file_fields_to_message.py
    │   └── 0006_create_indexes_increase_user_image_char_.py
├── docker-compose.yml
├── poetry.lock
├── pyproject.toml
├── src
    ├── admin
    │   ├── admin.py
    │   ├── authentication_backend.py
    │   ├── models.py
    │   └── services.py
    ├── authentication
    │   ├── router.py
    │   ├── schemas.py
    │   ├── services.py
    │   └── utils.py
    ├── chat
    │   ├── router.py
    │   ├── schemas.py
    │   └── services.py
    ├── config.py
    ├── contact
    │   ├── router.py
    │   ├── schemas.py
    │   └── services.py
    ├── database.py
    ├── dependencies.py
    ├── main.py
    ├── managers
    │   ├── pubsub_manager.py
    │   └── websocket_manager.py
    ├── models.py
    ├── registration
    │   ├── router.py
    │   ├── schemas.py
    │   └── services.py
    ├── routers.py
    ├── settings
    │   ├── router.py
    │   ├── schemas.py
    │   └── services.py
    ├── static
    │   └── .gitkeep
    ├── utils.py
    └── websocket
    │   ├── exceptions.py
    │   ├── handlers.py
    │   ├── rate_limiter.py
    │   ├── router.py
    │   ├── schemas.py
    │   └── services.py
├── templates
    └── index.html
└── tests
    ├── __init__.py
    ├── authentication
        └── test_authentication_router.py
    ├── chat
        ├── router
        │   ├── test_create_chat.py
        │   ├── test_get_chat_messages.py
        │   ├── test_get_older_messages.py
        │   └── test_get_user_chats.py
        └── services
        │   └── test_direct_chat_exists.py
    ├── conftest.py
    ├── contact
        └── test_contact_router.py
    ├── registration
        └── test_registration_router.py
    └── settings
        └── test_settings_router.py


/.dockerignore:
--------------------------------------------------------------------------------
 1 | media
 2 | .git
 3 | celerybeat.pid
 4 | celerybeat-schedule
 5 | venv
 6 | env
 7 | .coverage
 8 | coverage.xml
 9 | .cache
10 | .pytest_cache
11 | **/__pycache__
12 | .idea


--------------------------------------------------------------------------------
/.github/workflows/ci.yml:
--------------------------------------------------------------------------------
 1 | name: Lint and Tests
 2 | 
 3 | on: [push, pull_request]
 4 | 
 5 | jobs:
 6 |   lint:
 7 |     if: "!contains(github.event.head_commit.message, '--no-ci')"
 8 |     name: Run Ruff
 9 |     runs-on: ubuntu-latest
10 |     steps:
11 |       - uses: actions/checkout@v4
12 |       - run: pipx install poetry
13 |       - name: Install Python
14 |         uses: actions/setup-python@v5
15 |         with:
16 |           python-version: "3.11.3"
17 |       - name: Install dependencies
18 |         run: |
19 |           poetry env use "3.11.3"
20 |           poetry export --only lint --output lint-requirements.txt
21 |           pip install -r lint-requirements.txt
22 |       - name: Ruff Linter
23 |         run: ruff check --output-format=github .
24 |       - name: Ruff Formatter
25 |         run: ruff format . --check
26 |   test:
27 |     if: "!contains(github.event.head_commit.message, '--no-ci')"
28 |     name: Run Tests
29 |     needs: lint
30 |     runs-on: ubuntu-latest
31 |     services:
32 |       postgres:
33 |         image: postgres:15-alpine
34 |         env:
35 |           POSTGRES_USER: postgres
36 |           POSTGRES_PASSWORD: postgres
37 |           POSTGRES_DB: postgres
38 |         ports: ["5432:5432"]
39 |       redis:
40 |         image: redis:7.0.12-alpine
41 |         options: >-
42 |           --health-cmd "redis-cli ping"
43 |           --health-interval 10s
44 |           --health-timeout 5s
45 |           --health-retries 5
46 |         ports:
47 |           - 6379:6379
48 |     steps:
49 |       - uses: actions/checkout@v4
50 |       - run: pipx install poetry
51 |       - name: Install Python
52 |         uses: actions/setup-python@v5
53 |         with:
54 |           python-version: "3.11.3"
55 | 
56 |       - uses: actions/setup-python@v4
57 |         with:
58 |           python-version: 3.11.3
59 |       - run: |
60 |           poetry env use "3.11.3"
61 |           poetry install
62 |           poetry run pytest -x -n auto --dist loadfile
63 |         env:
64 |           DB_HOST: "localhost"
65 |           REDIS_HOST: "localhost"
66 |           REDIS_PORT: "6379"


--------------------------------------------------------------------------------
/.gitignore:
--------------------------------------------------------------------------------
  1 | # Byte-compiled / optimized / DLL files
  2 | __pycache__
  3 | __pycache__/
  4 | *.py[cod]
  5 | *$py.class
  6 | 
  7 | # VS Code
  8 | .vscode
  9 | 
 10 | # C extensions
 11 | *.so
 12 | 
 13 | # Distribution / packaging
 14 | .Python
 15 | build/
 16 | develop-eggs/
 17 | dist/
 18 | downloads/
 19 | eggs/
 20 | .eggs/
 21 | lib/
 22 | lib64/
 23 | parts/
 24 | sdist/
 25 | var/
 26 | wheels/
 27 | share/python-wheels/
 28 | *.egg-info/
 29 | .installed.cfg
 30 | *.egg
 31 | MANIFEST
 32 | 
 33 | # PyInstaller
 34 | #  Usually these files are written by a python script from a template
 35 | #  before PyInstaller builds the exe, so as to inject date/other infos into it.
 36 | *.manifest
 37 | *.spec
 38 | 
 39 | # Installer logs
 40 | pip-log.txt
 41 | pip-delete-this-directory.txt
 42 | 
 43 | # Unit test / coverage reports
 44 | htmlcov/
 45 | .tox/
 46 | .nox/
 47 | .coverage
 48 | .coverage.*
 49 | .cache
 50 | nosetests.xml
 51 | coverage.xml
 52 | *.cover
 53 | *.py,cover
 54 | .hypothesis/
 55 | .pytest_cache/
 56 | cover/
 57 | 
 58 | # Translations
 59 | *.mo
 60 | *.pot
 61 | 
 62 | # Django stuff:
 63 | *.log
 64 | local_settings.py
 65 | db.sqlite3
 66 | db.sqlite3-journal
 67 | 
 68 | # Flask stuff:
 69 | instance/
 70 | .webassets-cache
 71 | 
 72 | # Scrapy stuff:
 73 | .scrapy
 74 | 
 75 | # Sphinx documentation
 76 | docs/_build/
 77 | 
 78 | # PyBuilder
 79 | .pybuilder/
 80 | target/
 81 | 
 82 | # Jupyter Notebook
 83 | .ipynb_checkpoints
 84 | 
 85 | # IPython
 86 | profile_default/
 87 | ipython_config.py
 88 | 
 89 | # pyenv
 90 | #   For a library or package, you might want to ignore these files since the code is
 91 | #   intended to run in multiple environments; otherwise, check them in:
 92 | # .python-version
 93 | 
 94 | # pipenv
 95 | #   According to pypa/pipenv#598, it is recommended to include Pipfile.lock in version control.
 96 | #   However, in case of collaboration, if having platform-specific dependencies or dependencies
 97 | #   having no cross-platform support, pipenv may install dependencies that don't work, or not
 98 | #   install all needed dependencies.
 99 | #Pipfile.lock
100 | 
101 | # poetry
102 | #   Similar to Pipfile.lock, it is generally recommended to include poetry.lock in version control.
103 | #   This is especially recommended for binary packages to ensure reproducibility, and is more
104 | #   commonly ignored for libraries.
105 | #   https://python-poetry.org/docs/basic-usage/#commit-your-poetrylock-file-to-version-control
106 | #poetry.lock
107 | 
108 | # pdm
109 | #   Similar to Pipfile.lock, it is generally recommended to include pdm.lock in version control.
110 | #pdm.lock
111 | #   pdm stores project-wide configurations in .pdm.toml, but it is recommended to not include it
112 | #   in version control.
113 | #   https://pdm.fming.dev/#use-with-ide
114 | .pdm.toml
115 | 
116 | # PEP 582; used by e.g. github.com/David-OConnor/pyflow and github.com/pdm-project/pdm
117 | __pypackages__/
118 | 
119 | # Celery stuff
120 | celerybeat-schedule
121 | celerybeat.pid
122 | 
123 | # SageMath parsed files
124 | *.sage.py
125 | 
126 | # Environments
127 | .env
128 | .venv
129 | env/
130 | venv/
131 | ENV/
132 | env.bak/
133 | venv.bak/
134 | 
135 | # Spyder project settings
136 | .spyderproject
137 | .spyproject
138 | 
139 | # Rope project settings
140 | .ropeproject
141 | 
142 | # mkdocs documentation
143 | /site
144 | 
145 | # mypy
146 | .mypy_cache/
147 | .dmypy.json
148 | dmypy.json
149 | 
150 | # Pyre type checker
151 | .pyre/
152 | 
153 | # pytype static type analyzer
154 | .pytype/
155 | 
156 | # Cython debug symbols
157 | cython_debug/
158 | 
159 | # PyCharm
160 | #  JetBrains specific template is maintained in a separate JetBrains.gitignore that can
161 | #  be found at https://github.com/github/gitignore/blob/main/Global/JetBrains.gitignore
162 | #  and can be added to the global gitignore or merged into this file.  For a more nuclear
163 | #  option (not recommended) you can uncomment the following to ignore the entire idea folder.
164 | #.idea/


--------------------------------------------------------------------------------
/.pre-commit-config.yaml:
--------------------------------------------------------------------------------
 1 | repos:
 2 |   - repo: https://github.com/astral-sh/ruff-pre-commit
 3 |     # Ruff version.
 4 |     rev: v0.3.0
 5 |     hooks:
 6 |       # Run the linter.
 7 |       - id: ruff
 8 |         args: [ --fix ]
 9 |       # Run the formatter.
10 |       - id: ruff-format


--------------------------------------------------------------------------------
/Dockerfile:
--------------------------------------------------------------------------------
 1 | FROM python:3.11.6-slim
 2 | 
 3 | RUN apt-get update && apt-get -y upgrade
 4 | 
 5 | ENV \
 6 |     PYTHONFAULTHANDLER=1 \
 7 |     PYTHONUNBUFFERED=1 \
 8 |     PYTHONHASHSEED=random \
 9 |     PIP_NO_CACHE_DIR=1 \
10 |     PIP_DISABLE_PIP_VERSION_CHECK=1 \
11 |     PIP_DEFAULT_TIMEOUT=100
12 | 
13 | ENV \
14 |     POETRY_HOME="/opt/poetry" \
15 |     POETRY_NO_INTERACTION=1 \
16 |     POETRY_VERSION=1.5.1
17 | 
18 | EXPOSE 8000
19 | 
20 | WORKDIR /opt/chat
21 | 
22 | COPY poetry.lock pyproject.toml ./
23 | RUN pip install "poetry==$POETRY_VERSION"
24 | RUN poetry export --with test,lint --output requirements.txt
25 | RUN pip install --no-deps -r requirements.txt
26 | 
27 | COPY . .


--------------------------------------------------------------------------------
/LICENSE:
--------------------------------------------------------------------------------
 1 | MIT License
 2 | 
 3 | Copyright (c) 2024 Bekzod Mirahmedov
 4 | 
 5 | Permission is hereby granted, free of charge, to any person obtaining a copy
 6 | of this software and associated documentation files (the "Software"), to deal
 7 | in the Software without restriction, including without limitation the rights
 8 | to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 9 | copies of the Software, and to permit persons to whom the Software is
10 | furnished to do so, subject to the following conditions:
11 | 
12 | The above copyright notice and this permission notice shall be included in all
13 | copies or substantial portions of the Software.
14 | 
15 | THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
16 | IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
17 | FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
18 | AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
19 | LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
20 | OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
21 | SOFTWARE.
22 | 


--------------------------------------------------------------------------------
/Makefile:
--------------------------------------------------------------------------------
 1 | network:
 2 | 	docker network create --driver=bridge chat-net
 3 | 
 4 | build:
 5 | 	docker compose build
 6 | 
 7 | up:
 8 | 	docker compose up -d
 9 | 
10 | build-up:
11 | 	docker compose up -d --build
12 | 
13 | restart:
14 | 	docker compose down && docker compose up -d
15 | 
16 | logs:
17 | 	docker logs -f chat-backend
18 | 
19 | down:
20 | 	docker compose down
21 | 
22 | ruff:
23 | 	ruff format .
24 | 	ruff check . --fix
25 | 
26 | test:
27 | 	docker exec -it chat-backend python -m pytest -svv $(target)
28 | 
29 | ftest:
30 | 	docker exec -it chat-backend python -m pytest -x -n 2 --dist loadfile
31 | 
32 | test-integration:
33 | 	docker exec -it chat-backend python -m pytest -m "integration" -svv
34 | 
35 | revision:
36 | 	docker exec -it chat-backend alembic revision --autogenerate -m "${m}"
37 | 
38 | upgrade:
39 | 	docker exec -it chat-backend alembic upgrade head
40 | 
41 | downgrade:
42 | 	docker exec -it chat-backend alembic downgrade -${last}
43 | 
44 | migration-history:
45 | 	docker exec -it chat-backend alembic history
46 | 
47 | web-bash:
48 | 	docker exec -it chat-backend bash
49 | 
50 | postgres-bash:
51 | 	docker exec -it chat-postgres bash
52 | 
53 | redis-cli:
54 | 	docker exec -it chat-redis redis-cli
55 | 
56 | show-envs:
57 | 	docker exec chat-backend env
58 | 


--------------------------------------------------------------------------------
/Procfile:
--------------------------------------------------------------------------------
1 | release: alembic upgrade head
2 | web: gunicorn -w 4 -k uvicorn.workers.UvicornWorker src.main:app


--------------------------------------------------------------------------------
/README.md:
--------------------------------------------------------------------------------
 1 | 
 2 | # NOTICE
 3 | # I could no longer maintain custom domain, please use
 4 | https://vuetify-chat.vercel.app/
 5 | # to see the demo
 6 | 
 7 | 
 8 | 
 9 | # Real-time Chat App with FastAPI
10 | 
11 | Live Demo: https://vuetify-chat.vercel.app/
12 | 
13 | Swagger UI: https://fastapi-chat-2bfa6dd112d2.herokuapp.com/docs
14 | 
15 | Admin: https://fastapi-chat-2bfa6dd112d2.herokuapp.com/admin/ (login: `bekzod`, password: `bekzod`)
16 | 
17 | #### Frontend with Vue 3 and Vuetify 3, see <a href="https://github.com/notarious2/vuetify-chat">repo</a> 
18 | 
19 | ### Tech Stack:
20 | - FastAPI, Websockets, Pydantic 2
21 | - Postgres (asyncpg), async Redis (PubSub and Cache)
22 | - SQLAlchemy 2
23 | - Database migrations with Alembic
24 | - Poetry for dependency management, containerization with Docker/docker-compose
25 | - Linting and Formatting with <strong>Ruff</strong>
26 | 
27 | ### High level functionality description
28 | - Fully asynchronous: FastAPI, Postgres, Redis
29 | - JWT HTTP-only cookies authentication (access, refresh tokens)
30 | - Horizontal Scalability with Redis PubSub
31 | - Rate Limiting (Throttling)
32 | - FastAPI background tasks: 
33 |   - uploading photos to cloud 
34 |   - updating non-essential info
35 | - Message status confirmation: sending, sent and read
36 | - Read status tracking with one field per user per chat\u2028(last read message) instead of tracking \u2028individual message read status
37 | - User status tracking: online, inactive and offline
38 | - User `is typing` tracking
39 | - Google Oauth2 authentication:
40 |   - Frontend retrieves access token
41 |   - Backend verifies and gets user data
42 | - Store files in AWS S3 bucket in production and StaticFiles in local development
43 | - Modern Admin panel with `sqladmin`
44 | 
45 | ### Local Setup
46 | - Configure necessary variables in `src/config.py`
47 | - Create network: `make network`
48 | - Build and run: `make build-up` (runs on port: 8001)
49 | - Apply migrations: `make upgrade`
50 | - Follow logs: `make logs`
51 | - Run tests: `make test`
52 |   
53 | <img width="864" alt="Screenshot 2024-02-18 at 18 37 11" src="https://github.com/notarious2/fastapi-chat/assets/104051317/27df9a18-5131-4e39-a80e-103f8b9ba5e8">
54 | 


--------------------------------------------------------------------------------
/alembic.ini:
--------------------------------------------------------------------------------
  1 | # A generic, single database configuration.
  2 | 
  3 | [alembic]
  4 | # path to migration scripts
  5 | script_location = alembic
  6 | 
  7 | # template used to generate migration file names; The default value is %%(rev)s_%%(slug)s
  8 | # Uncomment the line below if you want the files to be prepended with date and time
  9 | # see https://alembic.sqlalchemy.org/en/latest/tutorial.html#editing-the-ini-file
 10 | # for all available tokens
 11 | # file_template = %%(year)d_%%(month).2d_%%(day).2d_%%(hour).2d%%(minute).2d-%%(rev)s_%%(slug)s
 12 | 
 13 | # sys.path path, will be prepended to sys.path if present.
 14 | # defaults to the current working directory.
 15 | prepend_sys_path = .
 16 | 
 17 | # timezone to use when rendering the date within the migration file
 18 | # as well as the filename.
 19 | # If specified, requires the python-dateutil library that can be
 20 | # installed by adding `alembic[tz]` to the pip requirements
 21 | # string value is passed to dateutil.tz.gettz()
 22 | # leave blank for localtime
 23 | # timezone =
 24 | 
 25 | # max length of characters to apply to the
 26 | # "slug" field
 27 | # truncate_slug_length = 40
 28 | 
 29 | # set to 'true' to run the environment during
 30 | # the 'revision' command, regardless of autogenerate
 31 | # revision_environment = false
 32 | 
 33 | # set to 'true' to allow .pyc and .pyo files without
 34 | # a source .py file to be detected as revisions in the
 35 | # versions/ directory
 36 | # sourceless = false
 37 | 
 38 | # version location specification; This defaults
 39 | # to alembic/versions.  When using multiple version
 40 | # directories, initial revisions must be specified with --version-path.
 41 | # The path separator used here should be the separator specified by "version_path_separator" below.
 42 | # version_locations = %(here)s/bar:%(here)s/bat:alembic/versions
 43 | 
 44 | # version path separator; As mentioned above, this is the character used to split
 45 | # version_locations. The default within new alembic.ini files is "os", which uses os.pathsep.
 46 | # If this key is omitted entirely, it falls back to the legacy behavior of splitting on spaces and/or commas.
 47 | # Valid values for version_path_separator are:
 48 | #
 49 | # version_path_separator = :
 50 | # version_path_separator = ;
 51 | # version_path_separator = space
 52 | version_path_separator = os  # Use os.pathsep. Default configuration used for new projects.
 53 | 
 54 | # set to 'true' to search source files recursively
 55 | # in each "version_locations" directory
 56 | # new in Alembic version 1.10
 57 | # recursive_version_locations = false
 58 | 
 59 | # the output encoding used when revision files
 60 | # are written from script.py.mako
 61 | # output_encoding = utf-8
 62 | 
 63 | sqlalchemy.url = driver://user:pass@localhost/dbname
 64 | 
 65 | 
 66 | [post_write_hooks]
 67 | # post_write_hooks defines scripts or Python functions that are run
 68 | # on newly generated revision scripts.  See the documentation for further
 69 | # detail and examples
 70 | 
 71 | # format using "black" - use the console_scripts runner, against the "black" entrypoint
 72 | #hooks = black
 73 | #black.type = console_scripts
 74 | #black.entrypoint = black
 75 | #black.options = -l 120 REVISION_SCRIPT_FILENAME
 76 | 
 77 | # lint with attempts to fix using "ruff" - use the exec runner, execute a binary
 78 | # hooks = ruff
 79 | # ruff.type = exec
 80 | # ruff.executable = %(here)s/.venv/bin/ruff
 81 | # ruff.options = --fix REVISION_SCRIPT_FILENAME
 82 | 
 83 | # Logging configuration
 84 | [loggers]
 85 | keys = root,sqlalchemy,alembic
 86 | 
 87 | [handlers]
 88 | keys = console
 89 | 
 90 | [formatters]
 91 | keys = generic
 92 | 
 93 | [logger_root]
 94 | level = WARN
 95 | handlers = console
 96 | qualname =
 97 | 
 98 | [logger_sqlalchemy]
 99 | level = WARN
100 | handlers =
101 | qualname = sqlalchemy.engine
102 | 
103 | [logger_alembic]
104 | level = INFO
105 | handlers =
106 | qualname = alembic
107 | 
108 | [handler_console]
109 | class = StreamHandler
110 | args = (sys.stderr,)
111 | level = NOTSET
112 | formatter = generic
113 | 
114 | [formatter_generic]
115 | format = %(levelname)-5.5s [%(name)s] %(message)s
116 | datefmt = %H:%M:%S
117 | 


--------------------------------------------------------------------------------
/alembic/README:
--------------------------------------------------------------------------------
1 | Generic single-database configuration.


--------------------------------------------------------------------------------
/alembic/env.py:
--------------------------------------------------------------------------------
  1 | import asyncio
  2 | from logging.config import fileConfig
  3 | 
  4 | from sqlalchemy import pool
  5 | from sqlalchemy.engine import Connection
  6 | from sqlalchemy.ext.asyncio import async_engine_from_config
  7 | 
  8 | from alembic import context
  9 | from alembic.script import ScriptDirectory
 10 | from src.admin.models import AdminUser  # noqa: F401
 11 | from src.database import DATABASE_URL, BaseModel
 12 | from src.models import Chat, Message, ReadStatus, User, chat_participant  # noqa: F401
 13 | 
 14 | # this is the Alembic Config object, which provides
 15 | # access to the values within the .ini file in use.
 16 | config = context.config
 17 | 
 18 | config.set_main_option("sqlalchemy.url", DATABASE_URL)
 19 | 
 20 | # Interpret the config file for Python logging.
 21 | # This line sets up loggers basically.
 22 | if config.config_file_name is not None:
 23 |     fileConfig(config.config_file_name)
 24 | 
 25 | # add your model's MetaData object here
 26 | # for 'autogenerate' support
 27 | # from myapp import mymodel
 28 | # target_metadata = mymodel.Base.metadata
 29 | target_metadata = BaseModel.metadata
 30 | 
 31 | # other values from the config, defined by the needs of env.py,
 32 | # can be acquired:
 33 | # my_important_option = config.get_main_option("my_important_option")
 34 | # ... etc.
 35 | 
 36 | 
 37 | def process_revision_directives(context, revision, directives):
 38 |     # extract Migration
 39 |     migration_script = directives[0]
 40 |     # extract current head revision
 41 |     head_revision = ScriptDirectory.from_config(context.config).get_current_head()
 42 | 
 43 |     if head_revision is None:
 44 |         # edge case with first migration
 45 |         new_rev_id = 1
 46 |     else:
 47 |         # default branch with incrementation
 48 |         last_rev_id = int(head_revision.lstrip("0"))
 49 |         new_rev_id = last_rev_id + 1
 50 |     # fill zeros up to 4 digits: 1 -> 0001
 51 |     migration_script.rev_id = "{0:04}".format(new_rev_id)
 52 | 
 53 | 
 54 | def run_migrations_offline() -> None:
 55 |     """Run migrations in 'offline' mode.
 56 | 
 57 |     This configures the context with just a URL
 58 |     and not an Engine, though an Engine is acceptable
 59 |     here as well.  By skipping the Engine creation
 60 |     we don't even need a DBAPI to be available.
 61 | 
 62 |     Calls to context.execute() here emit the given string to the
 63 |     script output.
 64 | 
 65 |     """
 66 |     url = config.get_main_option("sqlalchemy.url")
 67 |     context.configure(
 68 |         url=url,
 69 |         target_metadata=target_metadata,
 70 |         literal_binds=True,
 71 |         dialect_opts={"paramstyle": "named"},
 72 |         process_revision_directives=process_revision_directives,
 73 |         version_table_schema=target_metadata.schema,
 74 |         include_schemas=True,
 75 |     )
 76 | 
 77 |     with context.begin_transaction():
 78 |         context.execute(f"CREATE SCHEMA IF NOT EXISTS {target_metadata.schema}")
 79 |         context.run_migrations()
 80 | 
 81 | 
 82 | def do_run_migrations(connection: Connection) -> None:
 83 |     context.configure(
 84 |         connection=connection,
 85 |         target_metadata=target_metadata,
 86 |         process_revision_directives=process_revision_directives,
 87 |         version_table_schema=target_metadata.schema,
 88 |         include_schemas=True,
 89 |     )
 90 | 
 91 |     with context.begin_transaction():
 92 |         context.execute(f"CREATE SCHEMA IF NOT EXISTS {target_metadata.schema}")
 93 |         context.run_migrations()
 94 | 
 95 | 
 96 | async def run_async_migrations() -> None:
 97 |     """In this scenario we need to create an Engine
 98 |     and associate a connection with the context.
 99 | 
100 |     """
101 | 
102 |     connectable = async_engine_from_config(
103 |         config.get_section(config.config_ini_section, {}),
104 |         prefix="sqlalchemy.",
105 |         poolclass=pool.NullPool,
106 |     )
107 | 
108 |     async with connectable.connect() as connection:
109 |         await connection.run_sync(do_run_migrations)
110 | 
111 |     await connectable.dispose()
112 | 
113 | 
114 | def run_migrations_online() -> None:
115 |     """Run migrations in 'online' mode."""
116 | 
117 |     asyncio.run(run_async_migrations())
118 | 
119 | 
120 | if context.is_offline_mode():
121 |     run_migrations_offline()
122 | else:
123 |     run_migrations_online()
124 | 


--------------------------------------------------------------------------------
/alembic/script.py.mako:
--------------------------------------------------------------------------------
 1 | """${message}
 2 | 
 3 | Revision ID: ${up_revision}
 4 | Revises: ${down_revision | comma,n}
 5 | Create Date: ${create_date}
 6 | 
 7 | """
 8 | from typing import Sequence, Union
 9 | 
10 | from alembic import op
11 | import sqlalchemy as sa
12 | ${imports if imports else ""}
13 | 
14 | # revision identifiers, used by Alembic.
15 | revision: str = ${repr(up_revision)}
16 | down_revision: Union[str, None] = ${repr(down_revision)}
17 | branch_labels: Union[str, Sequence[str], None] = ${repr(branch_labels)}
18 | depends_on: Union[str, Sequence[str], None] = ${repr(depends_on)}
19 | 
20 | 
21 | def upgrade() -> None:
22 |     ${upgrades if upgrades else "pass"}
23 | 
24 | 
25 | def downgrade() -> None:
26 |     ${downgrades if downgrades else "pass"}
27 | 


--------------------------------------------------------------------------------
/alembic/versions/0001_create_models.py:
--------------------------------------------------------------------------------
  1 | """create models
  2 | 
  3 | Revision ID: 0001
  4 | Revises:
  5 | Create Date: 2023-09-11 17:34:46.924046
  6 | 
  7 | """
  8 | from typing import Sequence, Union
  9 | 
 10 | import sqlalchemy as sa
 11 | 
 12 | from alembic import op
 13 | 
 14 | # revision identifiers, used by Alembic.
 15 | revision: str = "0001"
 16 | down_revision: Union[str, None] = None
 17 | branch_labels: Union[str, Sequence[str], None] = None
 18 | depends_on: Union[str, Sequence[str], None] = None
 19 | 
 20 | 
 21 | def upgrade() -> None:
 22 |     # ### commands auto generated by Alembic - please adjust! ###
 23 |     op.create_table(
 24 |         "chat",
 25 |         sa.Column("id", sa.Integer(), autoincrement=True, nullable=False),
 26 |         sa.Column("guid", sa.UUID(), nullable=False),
 27 |         sa.Column(
 28 |             "chat_type", sa.Enum("DIRECT", "GROUP", name="chattype", schema="chat", inherit_schema=True), nullable=False
 29 |         ),
 30 |         sa.Column("created_at", sa.DateTime(timezone=True), server_default=sa.text("now()"), nullable=False),
 31 |         sa.Column("updated_at", sa.DateTime(timezone=True), server_default=sa.text("now()"), nullable=False),
 32 |         sa.Column("is_deleted", sa.Boolean(), nullable=False),
 33 |         sa.PrimaryKeyConstraint("id"),
 34 |         schema="chat",
 35 |     )
 36 |     op.create_table(
 37 |         "user",
 38 |         sa.Column("id", sa.Integer(), autoincrement=True, nullable=False),
 39 |         sa.Column("guid", sa.UUID(), nullable=False),
 40 |         sa.Column("password", sa.String(length=128), nullable=False),
 41 |         sa.Column("username", sa.String(length=150), nullable=False),
 42 |         sa.Column("first_name", sa.String(length=150), nullable=False),
 43 |         sa.Column("last_name", sa.String(length=150), nullable=False),
 44 |         sa.Column("email", sa.String(length=254), nullable=False),
 45 |         sa.Column("last_login", sa.DateTime(timezone=True), nullable=True),
 46 |         sa.Column("is_superuser", sa.Boolean(), nullable=False),
 47 |         sa.Column("user_image", sa.String(length=128), nullable=True),
 48 |         sa.Column("created_at", sa.DateTime(timezone=True), server_default=sa.text("now()"), nullable=False),
 49 |         sa.Column("updated_at", sa.DateTime(timezone=True), server_default=sa.text("now()"), nullable=False),
 50 |         sa.Column("is_deleted", sa.Boolean(), nullable=False),
 51 |         sa.PrimaryKeyConstraint("id"),
 52 |         sa.UniqueConstraint("email"),
 53 |         sa.UniqueConstraint("guid"),
 54 |         sa.UniqueConstraint("username"),
 55 |         schema="chat",
 56 |     )
 57 |     op.create_table(
 58 |         "chat_participant",
 59 |         sa.Column("user_id", sa.Integer(), nullable=False),
 60 |         sa.Column("chat_id", sa.Integer(), nullable=False),
 61 |         sa.ForeignKeyConstraint(
 62 |             ["chat_id"],
 63 |             ["chat.chat.id"],
 64 |         ),
 65 |         sa.ForeignKeyConstraint(
 66 |             ["user_id"],
 67 |             ["chat.user.id"],
 68 |         ),
 69 |         sa.PrimaryKeyConstraint("user_id", "chat_id"),
 70 |         schema="chat",
 71 |     )
 72 |     op.create_table(
 73 |         "message",
 74 |         sa.Column("id", sa.Integer(), autoincrement=True, nullable=False),
 75 |         sa.Column("guid", sa.UUID(), nullable=False),
 76 |         sa.Column("content", sa.String(length=5000), nullable=False),
 77 |         sa.Column("user_id", sa.Integer(), nullable=False),
 78 |         sa.Column("chat_id", sa.Integer(), nullable=False),
 79 |         sa.Column("created_at", sa.DateTime(timezone=True), server_default=sa.text("now()"), nullable=False),
 80 |         sa.Column("updated_at", sa.DateTime(timezone=True), server_default=sa.text("now()"), nullable=False),
 81 |         sa.Column("is_deleted", sa.Boolean(), nullable=False),
 82 |         sa.ForeignKeyConstraint(
 83 |             ["chat_id"],
 84 |             ["chat.chat.id"],
 85 |         ),
 86 |         sa.ForeignKeyConstraint(
 87 |             ["user_id"],
 88 |             ["chat.user.id"],
 89 |         ),
 90 |         sa.PrimaryKeyConstraint("id"),
 91 |         schema="chat",
 92 |     )
 93 |     op.create_table(
 94 |         "read_status",
 95 |         sa.Column("id", sa.Integer(), autoincrement=True, nullable=False),
 96 |         sa.Column("last_read_message_id", sa.Integer(), nullable=True),
 97 |         sa.Column("user_id", sa.Integer(), nullable=False),
 98 |         sa.Column("chat_id", sa.Integer(), nullable=False),
 99 |         sa.ForeignKeyConstraint(
100 |             ["chat_id"],
101 |             ["chat.chat.id"],
102 |         ),
103 |         sa.ForeignKeyConstraint(
104 |             ["user_id"],
105 |             ["chat.user.id"],
106 |         ),
107 |         sa.PrimaryKeyConstraint("id"),
108 |         schema="chat",
109 |     )
110 |     # ### end Alembic commands ###
111 | 
112 | 
113 | def downgrade() -> None:
114 |     # ### commands auto generated by Alembic - please adjust! ###
115 |     op.drop_table("read_status", schema="chat")
116 |     op.drop_table("message", schema="chat")
117 |     op.drop_table("chat_participant", schema="chat")
118 |     op.drop_table("user", schema="chat")
119 |     op.drop_table("chat", schema="chat")
120 |     sa.Enum("DIRECT", "GROUP", name="chattype", schema="chat", inherit_schema=True).drop(op.get_bind())
121 |     # ### end Alembic commands ###
122 | 


--------------------------------------------------------------------------------
/alembic/versions/0002_create_admin_user_model.py:
--------------------------------------------------------------------------------
 1 | """create admin user model
 2 | 
 3 | Revision ID: 0002
 4 | Revises: 0001
 5 | Create Date: 2023-11-23 15:01:40.287999
 6 | 
 7 | """
 8 | from typing import Sequence, Union
 9 | 
10 | import sqlalchemy as sa
11 | from sqlalchemy.sql import text
12 | 
13 | from alembic import op
14 | 
15 | # revision identifiers, used by Alembic.
16 | revision: str = "0002"
17 | down_revision: Union[str, None] = "0001"
18 | branch_labels: Union[str, Sequence[str], None] = None
19 | depends_on: Union[str, Sequence[str], None] = None
20 | 
21 | 
22 | def upgrade() -> None:
23 |     # ### commands auto generated by Alembic - please adjust! ###
24 |     op.create_table(
25 |         "admin_user",
26 |         sa.Column("id", sa.Integer(), autoincrement=True, nullable=False),
27 |         sa.Column("name", sa.String(length=50), nullable=False),
28 |         sa.Column("email", sa.String(length=254), nullable=False),
29 |         sa.Column("username", sa.String(length=150), nullable=False),
30 |         sa.Column("password", sa.String(length=128), nullable=False),
31 |         sa.Column("created_at", sa.DateTime(timezone=True), server_default=sa.text("now()"), nullable=False),
32 |         sa.Column("updated_at", sa.DateTime(timezone=True), server_default=sa.text("now()"), nullable=False),
33 |         sa.Column("is_deleted", sa.Boolean(), nullable=False),
34 |         sa.PrimaryKeyConstraint("id"),
35 |         sa.UniqueConstraint("email"),
36 |         sa.UniqueConstraint("username"),
37 |         schema="chat",
38 |     )
39 |     op.execute(
40 |         text(
41 |             """
42 |         INSERT INTO chat.admin_user (name, username, email, password, is_deleted)
43 |         VALUES
44 |         ('Bekzod', 'bekzod', 'bekzod@gmail.com',
45 |         '$2b$12$tp2weomut5QJjYnpA7UIJ.Go5V5QZAPziEzUjl/gK8b41UcjanG6O', False);
46 |         """
47 |         )
48 |     )
49 |     # ### end Alembic commands ###
50 | 
51 | 
52 | def downgrade() -> None:
53 |     # ### commands auto generated by Alembic - please adjust! ###
54 |     op.drop_table("admin_user", schema="chat")
55 |     # ### end Alembic commands ###
56 | 


--------------------------------------------------------------------------------
/alembic/versions/0003_increase_user_image_text_length.py:
--------------------------------------------------------------------------------
 1 | """increase user image text length
 2 | 
 3 | Revision ID: 0003
 4 | Revises: 0002
 5 | Create Date: 2023-12-27 17:57:32.554642
 6 | 
 7 | """
 8 | from typing import Sequence, Union
 9 | 
10 | import sqlalchemy as sa
11 | 
12 | from alembic import op
13 | 
14 | # revision identifiers, used by Alembic.
15 | revision: str = "0003"
16 | down_revision: Union[str, None] = "0002"
17 | branch_labels: Union[str, Sequence[str], None] = None
18 | depends_on: Union[str, Sequence[str], None] = None
19 | 
20 | 
21 | def upgrade() -> None:
22 |     # ### commands auto generated by Alembic - please adjust! ###
23 |     op.alter_column(
24 |         "user",
25 |         "user_image",
26 |         existing_type=sa.VARCHAR(length=128),
27 |         type_=sa.String(length=1000),
28 |         existing_nullable=True,
29 |         schema="chat",
30 |     )
31 |     # ### end Alembic commands ###
32 | 
33 | 
34 | def downgrade() -> None:
35 |     # ### commands auto generated by Alembic - please adjust! ###
36 |     op.alter_column(
37 |         "user",
38 |         "user_image",
39 |         existing_type=sa.String(length=1000),
40 |         type_=sa.VARCHAR(length=128),
41 |         existing_nullable=True,
42 |         schema="chat",
43 |     )
44 |     # ### end Alembic commands ###
45 | 


--------------------------------------------------------------------------------
/alembic/versions/0004_add_settings_to_user_model.py:
--------------------------------------------------------------------------------
 1 | """Add settings to user model
 2 | 
 3 | Revision ID: 0004
 4 | Revises: 0003
 5 | Create Date: 2024-01-02 06:03:42.656455
 6 | 
 7 | """
 8 | from typing import Sequence, Union
 9 | 
10 | import sqlalchemy as sa
11 | from sqlalchemy.dialects import postgresql
12 | 
13 | from alembic import op
14 | 
15 | # revision identifiers, used by Alembic.
16 | revision: str = "0004"
17 | down_revision: Union[str, None] = "0003"
18 | branch_labels: Union[str, Sequence[str], None] = None
19 | depends_on: Union[str, Sequence[str], None] = None
20 | 
21 | 
22 | def upgrade() -> None:
23 |     # ### commands auto generated by Alembic - please adjust! ###
24 |     op.add_column(
25 |         "user",
26 |         sa.Column("settings", postgresql.JSONB(astext_type=sa.Text()), server_default="{}", nullable=False),
27 |         schema="chat",
28 |     )
29 |     # ### end Alembic commands ###
30 | 
31 | 
32 | def downgrade() -> None:
33 |     # ### commands auto generated by Alembic - please adjust! ###
34 |     op.drop_column("user", "settings", schema="chat")
35 |     # ### end Alembic commands ###
36 | 


--------------------------------------------------------------------------------
/alembic/versions/0005_add_type_and_file_fields_to_message.py:
--------------------------------------------------------------------------------
 1 | """Add type and file fields to Message
 2 | 
 3 | Revision ID: 0005
 4 | Revises: 0004
 5 | Create Date: 2024-01-17 08:09:18.011913
 6 | 
 7 | """
 8 | from typing import Sequence, Union
 9 | 
10 | import sqlalchemy as sa
11 | 
12 | from alembic import op
13 | 
14 | # revision identifiers, used by Alembic.
15 | revision: str = "0005"
16 | down_revision: Union[str, None] = "0004"
17 | branch_labels: Union[str, Sequence[str], None] = None
18 | depends_on: Union[str, Sequence[str], None] = None
19 | 
20 | 
21 | def upgrade() -> None:
22 |     # ### commands auto generated by Alembic - please adjust! ###
23 |     messagetype_enum = sa.Enum("TEXT", "FILE", name="messagetype", schema="chat", inherit_schema=True)
24 |     messagetype_enum.create(op.get_bind(), checkfirst=True)
25 |     op.add_column(
26 |         "message", sa.Column("message_type", messagetype_enum, nullable=False, server_default="TEXT"), schema="chat"
27 |     )
28 |     op.add_column("message", sa.Column("file_name", sa.String(length=50), nullable=True), schema="chat")
29 |     op.add_column("message", sa.Column("file_path", sa.String(length=1000), nullable=True), schema="chat")
30 |     # ### end Alembic commands ###
31 | 
32 | 
33 | def downgrade() -> None:
34 |     # ### commands auto generated by Alembic - please adjust! ###
35 |     op.drop_column("message", "file_path", schema="chat")
36 |     op.drop_column("message", "file_name", schema="chat")
37 |     op.drop_column("message", "message_type", schema="chat")
38 |     sa.Enum("TEXT", "FILE", name="messagetype", schema="chat", inherit_schema=True).drop(op.get_bind())
39 |     # ### end Alembic commands ###
40 | 


--------------------------------------------------------------------------------
/alembic/versions/0006_create_indexes_increase_user_image_char_.py:
--------------------------------------------------------------------------------
  1 | """Create indexes, Increase user_image char length
  2 | 
  3 | Revision ID: 0006
  4 | Revises: 0005
  5 | Create Date: 2024-02-25 04:37:53.273850
  6 | 
  7 | """
  8 | from typing import Sequence, Union
  9 | 
 10 | import sqlalchemy as sa
 11 | 
 12 | from alembic import op
 13 | 
 14 | # revision identifiers, used by Alembic.
 15 | revision: str = "0006"
 16 | down_revision: Union[str, None] = "0005"
 17 | branch_labels: Union[str, Sequence[str], None] = None
 18 | depends_on: Union[str, Sequence[str], None] = None
 19 | 
 20 | 
 21 | def upgrade() -> None:
 22 |     op.execute("COMMIT")  # to run concurrently
 23 |     # ### commands auto generated by Alembic - please adjust! ###
 24 |     op.create_index(
 25 |         "idx_chat_on_guid",
 26 |         "chat",
 27 |         ["guid"],
 28 |         unique=False,
 29 |         schema="chat",
 30 |         postgresql_concurrently=True,
 31 |         if_not_exists=True,
 32 |     )
 33 |     op.create_index(
 34 |         "idx_chat_on_is_deleted_chat_type",
 35 |         "chat",
 36 |         ["is_deleted", "chat_type"],
 37 |         unique=False,
 38 |         schema="chat",
 39 |         postgresql_concurrently=True,
 40 |         if_not_exists=True,
 41 |     )
 42 |     op.create_index(
 43 |         "idx_message_on_chat_id",
 44 |         "message",
 45 |         ["chat_id"],
 46 |         unique=False,
 47 |         schema="chat",
 48 |         postgresql_concurrently=True,
 49 |         if_not_exists=True,
 50 |     )
 51 |     op.create_index(
 52 |         "idx_message_on_user_id",
 53 |         "message",
 54 |         ["user_id"],
 55 |         unique=False,
 56 |         schema="chat",
 57 |         postgresql_concurrently=True,
 58 |         if_not_exists=True,
 59 |     )
 60 |     op.create_index(
 61 |         "idx_message_on_user_id_chat_id",
 62 |         "message",
 63 |         ["chat_id", "user_id"],
 64 |         unique=False,
 65 |         schema="chat",
 66 |         postgresql_concurrently=True,
 67 |         if_not_exists=True,
 68 |     )
 69 |     op.create_index(
 70 |         "idx_read_status_on_chat_id",
 71 |         "read_status",
 72 |         ["chat_id"],
 73 |         unique=False,
 74 |         schema="chat",
 75 |         postgresql_concurrently=True,
 76 |         if_not_exists=True,
 77 |     )
 78 |     op.create_index(
 79 |         "idx_read_status_on_user_id",
 80 |         "read_status",
 81 |         ["user_id"],
 82 |         unique=False,
 83 |         schema="chat",
 84 |         postgresql_concurrently=True,
 85 |         if_not_exists=True,
 86 |     )
 87 |     op.alter_column(
 88 |         "user",
 89 |         "user_image",
 90 |         existing_type=sa.VARCHAR(length=1000),
 91 |         type_=sa.String(length=1048),
 92 |         existing_nullable=True,
 93 |         schema="chat",
 94 |     )
 95 |     op.create_index(
 96 |         "idx_user_on_email_username",
 97 |         "user",
 98 |         ["email", "username"],
 99 |         unique=False,
100 |         schema="chat",
101 |         postgresql_concurrently=True,
102 |         if_not_exists=True,
103 |     )
104 |     op.create_index(
105 |         "idx_user_partial_on_email_not_deleted",
106 |         "user",
107 |         ["email", "is_deleted"],
108 |         unique=False,
109 |         schema="chat",
110 |         postgresql_concurrently=True,
111 |         postgresql_where=sa.text("is_deleted IS false"),
112 |         if_not_exists=True,
113 |     )
114 |     # ### end Alembic commands ###
115 | 
116 | 
117 | def downgrade() -> None:
118 |     op.execute("COMMIT")  # to run concurrently
119 |     # ### commands auto generated by Alembic - please adjust! ###
120 |     op.drop_index(
121 |         "idx_user_partial_on_email_not_deleted",
122 |         table_name="user",
123 |         schema="chat",
124 |         postgresql_concurrently=True,
125 |         postgresql_where=sa.text("is_deleted IS false"),
126 |         if_exists=True,
127 |     )
128 |     op.drop_index(
129 |         "idx_user_on_email_username", table_name="user", schema="chat", postgresql_concurrently=True, if_exists=True
130 |     )
131 |     op.alter_column(
132 |         "user",
133 |         "user_image",
134 |         existing_type=sa.String(length=1048),
135 |         type_=sa.VARCHAR(length=1000),
136 |         existing_nullable=True,
137 |         schema="chat",
138 |     )
139 |     op.drop_index(
140 |         "idx_read_status_on_user_id",
141 |         table_name="read_status",
142 |         schema="chat",
143 |         postgresql_concurrently=True,
144 |         if_exists=True,
145 |     )
146 |     op.drop_index(
147 |         "idx_read_status_on_chat_id",
148 |         table_name="read_status",
149 |         schema="chat",
150 |         postgresql_concurrently=True,
151 |         if_exists=True,
152 |     )
153 |     op.drop_index(
154 |         "idx_message_on_user_id_chat_id",
155 |         table_name="message",
156 |         schema="chat",
157 |         postgresql_concurrently=True,
158 |         if_exists=True,
159 |     )
160 |     op.drop_index(
161 |         "idx_message_on_user_id", table_name="message", schema="chat", postgresql_concurrently=True, if_exists=True
162 |     )
163 |     op.drop_index(
164 |         "idx_message_on_chat_id", table_name="message", schema="chat", postgresql_concurrently=True, if_exists=True
165 |     )
166 |     op.drop_index(
167 |         "idx_chat_on_is_deleted_chat_type",
168 |         table_name="chat",
169 |         schema="chat",
170 |         postgresql_concurrently=True,
171 |         if_exists=True,
172 |     )
173 |     op.drop_index("idx_chat_on_guid", table_name="chat", schema="chat", postgresql_concurrently=True, if_exists=True)
174 |     # ### end Alembic commands ###
175 | 


--------------------------------------------------------------------------------
/docker-compose.yml:
--------------------------------------------------------------------------------
 1 | version: "3.8"
 2 | 
 3 | services:
 4 |   web:
 5 |     build:
 6 |       context: .
 7 |       dockerfile: Dockerfile
 8 |     container_name: chat-backend
 9 |     environment:
10 |       - ENVIRONMENT=development
11 |     ports:
12 |       - "8001:8001"
13 |     volumes:
14 |       - .:/opt/chat
15 |     depends_on:
16 |       - db
17 |     networks:
18 |       - chat-net
19 |     command: uvicorn src.main:app --host=0.0.0.0 --port=8001 --reload
20 | 
21 |   db:
22 |     image: postgres:15-alpine
23 |     container_name: chat-postgres
24 |     restart: always
25 |     environment:
26 |       POSTGRES_USER: postgres
27 |       POSTGRES_PASSWORD: postgres
28 |       POSTGRES_DB: postgres
29 |     ports:
30 |       - 5442:5432
31 |     volumes:
32 |       - postgres_data:/var/lib/postgresql/data/
33 |     networks:
34 |       - chat-net
35 |     command: ["postgres", "-c", "log_statement=all"]  # show logs
36 | 
37 |   redis-service:
38 |     image: redis
39 |     container_name: chat-redis
40 |     restart: always
41 |     ports:
42 |       - "6379:6379"
43 |     networks:
44 |       - chat-net
45 | 
46 |   pgadmin:
47 |     container_name: chat-pgadmin
48 |     image: dpage/pgadmin4
49 |     restart: always
50 |     environment:
51 |       PGADMIN_DEFAULT_EMAIL: admin@admin.com
52 |       PGADMIN_DEFAULT_PASSWORD: admin
53 |     ports:
54 |       - "7070:80"
55 |     volumes:
56 |       - pgadmin_data:/var/lib/pgadmin
57 |     networks:
58 |       - chat-net
59 | 
60 | networks:
61 |   chat-net:
62 |     external: true
63 | 
64 | volumes:
65 |   postgres_data:
66 |   pgadmin_data:
67 | 


--------------------------------------------------------------------------------
/pyproject.toml:
--------------------------------------------------------------------------------
 1 | [tool.poetry]
 2 | name = "chat-backend"
 3 | version = "0.1.0"
 4 | description = ""
 5 | authors = ["Bekzod Mirahmedov <notarious2@gmail.com>"]
 6 | readme = "README.md"
 7 | 
 8 | 
 9 | [tool.poetry.dependencies]
10 | python = "3.11.3"
11 | fastapi = "^0.103.1"
12 | pydantic = {extras = ["email"], version = "^2.3.0"}
13 | uvicorn = "^0.23.2"
14 | websockets = "^11.0.3"
15 | alembic = "^1.12.0"
16 | sqlalchemy = "^2.0.20"
17 | pydantic-settings = "^2.0.3"
18 | redis = "<5.0.0"
19 | asyncpg = "^0.28.0"
20 | python-jose = {extras = ["cryptography"], version = "^3.3.0"}
21 | passlib = {extras = ["bcrypt"], version = "^1.7.4"}
22 | httpx = "^0.25.0"
23 | pyjwt = "^2.8.0"
24 | python-multipart = "^0.0.6"
25 | fastapi-pagination = "^0.12.9"
26 | fastapi-limiter = "^0.1.5"
27 | asgi-lifespan = "^2.1.0"
28 | sqladmin = "^0.16.0"
29 | itsdangerous = "^2.1.2"
30 | gunicorn = "^21.2.0"
31 | aiofiles = "^23.2.1"
32 | boto3 = "^1.33.9"
33 | Pillow = "10.1.0"
34 | sentry-sdk = {extras = ["asyncpg", "fastapi"], version = "^1.39.1"}
35 | uvloop = "0.19.0"
36 | httptools = "0.6.1"
37 | 
38 | [tool.poetry.group.test.dependencies]
39 | pytest = "7.4.3"
40 | pytest-asyncio = "0.23.5"
41 | pytest-env = "1.1.3"
42 | pytest-mock = "3.12.0"
43 | pytest-xdist = "3.5.0"
44 | 
45 | [tool.poetry.group.lint.dependencies]
46 | ruff = "0.3.0"
47 | 
48 | [build-system]
49 | requires = ["poetry-core"]
50 | build-backend = "poetry.core.masonry.api"
51 | 
52 | [tool.ruff]
53 | line-length = 120
54 | exclude = ["alembic"]
55 | 
56 | [tool.ruff.lint]
57 | extend-select = ["W", "E", "I"]
58 | preview = true
59 | 
60 | [tool.pytest.ini_options]
61 | filterwarnings = ["ignore::DeprecationWarning"]
62 | asyncio_mode="auto"
63 | markers = [
64 |     "integration: Custom mark for integration tests"
65 | ]
66 | env = [
67 |     "ENVIRONMENT = test",
68 | ]


--------------------------------------------------------------------------------
/src/admin/admin.py:
--------------------------------------------------------------------------------
 1 | from sqladmin import ModelView
 2 | 
 3 | from src.models import Chat, Message, ReadStatus, User
 4 | 
 5 | 
 6 | class ChatAdmin(ModelView, model=Chat):
 7 |     icon = "fa-solid fa-comments"
 8 |     column_list = [Chat.id, Chat.chat_type, Chat.users, Chat.guid, Chat.is_deleted, Chat.created_at]
 9 | 
10 | 
11 | class MessageAdmin(ModelView, model=Message):
12 |     icon = "fas fa-commenting"
13 |     column_list = [Message.id, Message.content, Message.user, Message.chat, Message.guid, Message.created_at]
14 | 
15 | 
16 | class UserAdmin(ModelView, model=User):
17 |     icon = "fa-solid fa-user"
18 |     column_list = [User.id, User.email, User.username, User.guid, User.is_deleted, User.created_at]
19 | 
20 | 
21 | class ReadStatusAdmin(ModelView, model=ReadStatus):
22 |     icon = "fas fa-check-double"
23 |     name_plural = "Read Statuses"
24 |     column_list = [ReadStatus.id, ReadStatus.user, ReadStatus.chat]
25 | 
26 | 
27 | admin_models = [UserAdmin, ChatAdmin, MessageAdmin, ReadStatusAdmin]
28 | 


--------------------------------------------------------------------------------
/src/admin/authentication_backend.py:
--------------------------------------------------------------------------------
 1 | from datetime import timedelta
 2 | from typing import Optional
 3 | 
 4 | from fastapi import HTTPException
 5 | from sqladmin.authentication import AuthenticationBackend
 6 | from starlette.requests import Request
 7 | from starlette.responses import RedirectResponse
 8 | 
 9 | from src.admin.models import AdminUser
10 | from src.admin.services import authenticate_admin_user, verify_admin_user_by_token
11 | from src.authentication.utils import create_access_token
12 | from src.config import settings
13 | from src.database import async_session_maker
14 | 
15 | ACCESS_TOKEN_EXPIRE_MINUTES = 60 * 24
16 | 
17 | 
18 | class AdminAuth(AuthenticationBackend):
19 |     async def login(self, request: Request) -> bool:
20 |         form = await request.form()
21 |         login_identifier, password = form["username"], form["password"]
22 | 
23 |         # Validate username/password credentials
24 |         async with async_session_maker() as db_session:
25 |             admin: AdminUser | None = await authenticate_admin_user(
26 |                 db_session=db_session, login_identifier=login_identifier, password=password
27 |             )
28 | 
29 |         if not admin:
30 |             raise HTTPException(status_code=401, detail="Invalid Credentials")
31 | 
32 |         access_token = create_access_token(
33 |             subject=login_identifier, expires_delta=timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
34 |         )
35 | 
36 |         request.session.update({"token": f"{access_token}"})
37 |         return True
38 | 
39 |     async def logout(self, request: Request) -> bool:
40 |         request.session.clear()
41 |         return True
42 | 
43 |     async def authenticate(self, request: Request) -> Optional[RedirectResponse]:
44 |         token = request.session.get("token")
45 | 
46 |         if not token:
47 |             return RedirectResponse(request.url_for("admin:login"), status_code=302)
48 | 
49 |         # Check the token
50 |         async with async_session_maker() as db_session:
51 |             await verify_admin_user_by_token(token=token, db_session=db_session, request=request)
52 | 
53 |         return True
54 | 
55 | 
56 | authentication_backend = AdminAuth(secret_key=settings.ADMIN_SECRET_KEY)
57 | 


--------------------------------------------------------------------------------
/src/admin/models.py:
--------------------------------------------------------------------------------
 1 | from sqlalchemy import String
 2 | from sqlalchemy.orm import Mapped, mapped_column
 3 | 
 4 | from src.database import BaseModel
 5 | 
 6 | 
 7 | class AdminUser(BaseModel):
 8 |     __tablename__ = "admin_user"
 9 | 
10 |     id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
11 |     name: Mapped[str] = mapped_column(String(50))
12 |     email: Mapped[str] = mapped_column(String(254), unique=True)
13 |     username: Mapped[str] = mapped_column(String(150), unique=True)
14 |     password: Mapped[str] = mapped_column(String(128))
15 | 


--------------------------------------------------------------------------------
/src/admin/services.py:
--------------------------------------------------------------------------------
 1 | import asyncio
 2 | from contextlib import contextmanager
 3 | 
 4 | import jwt
 5 | from fastapi import Depends, HTTPException, status
 6 | from sqlalchemy import or_, select
 7 | from sqlalchemy.ext.asyncio import AsyncSession
 8 | from starlette.requests import Request
 9 | 
10 | from src.admin.models import AdminUser
11 | from src.config import settings
12 | from src.database import get_async_session
13 | from src.utils import verify_password
14 | 
15 | 
16 | # Authenticate admin based on username/email and password
17 | async def authenticate_admin_user(db_session: AsyncSession, login_identifier: str, password: str) -> AdminUser | None:
18 |     # Introduce a small delay to mitigate user enumeration attacks
19 |     await asyncio.sleep(0.1)
20 | 
21 |     query = select(AdminUser).where(or_(AdminUser.email == login_identifier, AdminUser.username == login_identifier))
22 |     result = await db_session.execute(query)
23 |     admin: AdminUser | None = result.scalar_one_or_none()
24 | 
25 |     if not admin or admin.is_deleted:
26 |         return None
27 | 
28 |     # if admin is found check password
29 |     if not verify_password(plain_password=password, hashed_password=admin.password):
30 |         return None
31 | 
32 |     return admin
33 | 
34 | 
35 | @contextmanager
36 | def clear_session_on_exception(request):
37 |     try:
38 |         yield
39 |     except (jwt.PyJWTError, HTTPException) as e:
40 |         request.session.clear()
41 |         raise e
42 | 
43 | 
44 | async def verify_admin_user_by_token(
45 |     token: str,
46 |     request: Request,
47 |     db_session: AsyncSession = Depends(get_async_session),
48 | ) -> None:
49 |     with clear_session_on_exception(request):
50 |         try:
51 |             payload = jwt.decode(token, settings.JWT_ACCESS_SECRET_KEY, algorithms=[settings.ENCRYPTION_ALGORITHM])
52 |             login_identifier: str = payload.get("sub")
53 |             if not login_identifier:
54 |                 raise HTTPException(
55 |                     status_code=status.HTTP_401_UNAUTHORIZED,
56 |                     detail="Could not validate credentials",
57 |                     headers={"WWW-Authenticate": "Bearer"},
58 |                 )
59 |         except jwt.ExpiredSignatureError:
60 |             raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Token expired")
61 |         except jwt.InvalidTokenError:
62 |             raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid Access Token")
63 |         except jwt.PyJWTError:
64 |             raise HTTPException(
65 |                 status_code=status.HTTP_401_UNAUTHORIZED,
66 |                 detail="Could not validate credentials",
67 |                 headers={"WWW-Authenticate": "Bearer"},
68 |             )
69 | 
70 |         statement = select(AdminUser).where(
71 |             or_(AdminUser.email == login_identifier, AdminUser.username == login_identifier)
72 |         )
73 |         result = await db_session.execute(statement)
74 |         admin: AdminUser | None = result.scalar_one_or_none()
75 | 
76 |         if not admin:
77 |             raise HTTPException(
78 |                 status_code=status.HTTP_401_UNAUTHORIZED,
79 |                 detail="Could not validate credentials",
80 |                 headers={"WWW-Authenticate": "Bearer"},
81 |             )
82 | 
83 |         if admin.is_deleted:
84 |             raise HTTPException(
85 |                 status_code=status.HTTP_401_UNAUTHORIZED,
86 |                 detail="Admin is not active",
87 |                 headers={"WWW-Authenticate": "Bearer"},
88 |             )
89 | 


--------------------------------------------------------------------------------
/src/authentication/router.py:
--------------------------------------------------------------------------------
  1 | import logging
  2 | from datetime import datetime, timedelta
  3 | from typing import Annotated
  4 | 
  5 | import jwt
  6 | from fastapi import APIRouter, BackgroundTasks, Cookie, Depends, HTTPException, Response, status
  7 | from fastapi.security import OAuth2PasswordRequestForm
  8 | from fastapi_limiter.depends import RateLimiter
  9 | from sqlalchemy.ext.asyncio import AsyncSession
 10 | 
 11 | from src.authentication.schemas import GoogleLoginSchema, UserLoginResponseSchema
 12 | from src.authentication.services import (
 13 |     authenticate_user,
 14 |     create_user_from_google_credentials,
 15 |     get_user_by_email,
 16 |     get_user_by_login_identifier,
 17 |     update_user_last_login,
 18 |     verify_google_token,
 19 | )
 20 | from src.authentication.utils import create_access_token, create_refresh_token
 21 | from src.config import settings
 22 | from src.database import get_async_session
 23 | from src.dependencies import get_current_user
 24 | from src.models import User
 25 | 
 26 | logger = logging.getLogger(__name__)
 27 | 
 28 | auth_router = APIRouter(tags=["Authentication"])
 29 | 
 30 | 
 31 | @auth_router.post(
 32 |     "/google-login/",
 33 |     dependencies=[
 34 |         Depends(RateLimiter(times=50, hours=24)),  # keep lower of result on top
 35 |         Depends(RateLimiter(times=10, minutes=1)),
 36 |     ],
 37 |     summary="Login with Google oauth2",
 38 | )
 39 | async def login_with_google(
 40 |     response: Response,
 41 |     google_login_schema: GoogleLoginSchema,
 42 |     background_tasks: BackgroundTasks,
 43 |     db_session: AsyncSession = Depends(get_async_session),
 44 | ):
 45 |     google_access_token: str = google_login_schema.access_token
 46 |     user_info: dict[str, str] | None = await verify_google_token(google_access_token)
 47 | 
 48 |     if not user_info:
 49 |         raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Could not verify Google credentials")
 50 | 
 51 |     # email field is case insensitive, db holds only lower case representation
 52 |     email: str = user_info.get("email", "").lower()
 53 |     if not email:
 54 |         HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Email was not provided")
 55 | 
 56 |     if not (user := await get_user_by_email(db_session, email=email)):
 57 |         user: User = await create_user_from_google_credentials(db_session, **user_info)
 58 | 
 59 |     else:
 60 |         # update last login for existing user
 61 |         background_tasks.add_task(update_user_last_login, db_session, user=user)
 62 | 
 63 |     access_token: str = create_access_token(email)
 64 |     refresh_token: str = create_refresh_token(email)
 65 | 
 66 |     # Send access and refresh tokens in HTTP only cookies
 67 |     response.set_cookie(key="access_token", value=access_token, httponly=True, samesite="none", secure=True)
 68 |     response.set_cookie(key="refresh_token", value=refresh_token, httponly=True, samesite="none", secure=True)
 69 | 
 70 |     login_response = UserLoginResponseSchema.model_validate(user)
 71 |     return login_response
 72 | 
 73 | 
 74 | @auth_router.post(
 75 |     "/login/",
 76 |     dependencies=[
 77 |         Depends(RateLimiter(times=50, hours=24)),  # keep lower of result on top
 78 |         Depends(RateLimiter(times=10, minutes=1)),
 79 |     ],
 80 |     summary="Create access and refresh tokens for a user",
 81 | )
 82 | async def login(
 83 |     response: Response,
 84 |     background_tasks: BackgroundTasks,
 85 |     db_session: AsyncSession = Depends(get_async_session),
 86 |     form_data: OAuth2PasswordRequestForm = Depends(),
 87 | ):
 88 |     login_identifier, password = form_data.username.lower(), form_data.password
 89 |     user: User | None = await authenticate_user(
 90 |         db_session=db_session, login_identifier=login_identifier, password=password
 91 |     )
 92 | 
 93 |     if not user:
 94 |         raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail="Incorrect email/username or password")
 95 | 
 96 |     # create token based on login identifier instead of static username/email
 97 |     access_token: str = create_access_token(login_identifier)
 98 |     refresh_token: str = create_refresh_token(login_identifier)
 99 | 
100 |     logger.debug(f"Access token: {access_token}")
101 | 
102 |     response.set_cookie(key="access_token", value=access_token, httponly=True, samesite="none", secure=True)
103 |     response.set_cookie(key="refresh_token", value=refresh_token, httponly=True, samesite="none", secure=True)
104 | 
105 |     # update last login date
106 |     background_tasks.add_task(update_user_last_login, db_session, user=user)
107 | 
108 |     login_response = UserLoginResponseSchema.model_validate(user)
109 |     return login_response
110 | 
111 | 
112 | @auth_router.post("/refresh/", summary="Create new access token for user")
113 | async def get_new_access_token_from_refresh_token(
114 |     refresh_token: Annotated[str, Cookie()],
115 |     response: Response,
116 |     db_session: AsyncSession = Depends(get_async_session),
117 | ):
118 |     try:
119 |         payload = jwt.decode(refresh_token, settings.JWT_REFRESH_SECRET_KEY, algorithms=[settings.ENCRYPTION_ALGORITHM])
120 |         login_identifier: str = payload.get("sub")
121 |         if not login_identifier:
122 |             raise HTTPException(
123 |                 status_code=status.HTTP_401_UNAUTHORIZED,
124 |                 detail="Could not validate credentials",
125 |                 headers={"WWW-Authenticate": "Bearer"},
126 |             )
127 |     except jwt.ExpiredSignatureError:
128 |         raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Token expired")
129 |     except jwt.InvalidTokenError:
130 |         raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid Access Token")
131 |     except jwt.PyJWTError:
132 |         raise HTTPException(
133 |             status_code=status.HTTP_401_UNAUTHORIZED,
134 |             detail="Could not validate credentials",
135 |             headers={"WWW-Authenticate": "Bearer"},
136 |         )
137 | 
138 |     user: User | None = await get_user_by_login_identifier(db_session, login_identifier=login_identifier)
139 | 
140 |     if not user:
141 |         raise HTTPException(
142 |             status_code=status.HTTP_401_UNAUTHORIZED,
143 |             detail="Could not validate credentials",
144 |             headers={"WWW-Authenticate": "Bearer"},
145 |         )
146 | 
147 |     if user.is_deleted:
148 |         raise HTTPException(
149 |             status_code=status.HTTP_401_UNAUTHORIZED,
150 |             detail="User is not active",
151 |             headers={"WWW-Authenticate": "Bearer"},
152 |         )
153 | 
154 |     new_access_token = create_access_token(
155 |         login_identifier, expires_delta=timedelta(minutes=settings.NEW_ACCESS_TOKEN_EXPIRE_MINUTES)
156 |     )
157 | 
158 |     response.set_cookie(key="access_token", value=new_access_token, httponly=True, samesite="none", secure=True)
159 | 
160 |     return "Access token has been successfully refreshed"
161 | 
162 | 
163 | @auth_router.get("/logout/", summary="Logout by removing http-only cookies")
164 | async def logout(response: Response, current_user: User = Depends(get_current_user)):
165 |     expires = datetime.utcnow() + timedelta(seconds=1)
166 |     response.set_cookie(
167 |         key="access_token",
168 |         value="",
169 |         secure=True,
170 |         httponly=True,
171 |         samesite="none",
172 |         expires=expires.strftime("%a, %d %b %Y %H:%M:%S GMT"),
173 |     )
174 |     response.set_cookie(
175 |         key="refresh_token",
176 |         value="",
177 |         secure=True,
178 |         httponly=True,
179 |         samesite="none",
180 |         expires=expires.strftime("%a, %d %b %Y %H:%M:%S GMT"),
181 |     )
182 |     # this doesn't work, must expire
183 |     # response.delete_cookie("access_token")
184 |     # response.delete_cookie("refresh_token")
185 |     return "Cookies removed"
186 | 


--------------------------------------------------------------------------------
/src/authentication/schemas.py:
--------------------------------------------------------------------------------
 1 | from pydantic import UUID4, BaseModel, EmailStr, Field, field_validator
 2 | 
 3 | from src.config import settings
 4 | 
 5 | 
 6 | class GoogleLoginSchema(BaseModel):
 7 |     access_token: str
 8 | 
 9 | 
10 | class UserLoginResponseSchema(BaseModel):
11 |     guid: UUID4 = Field(..., serialization_alias="user_guid")
12 |     username: str
13 |     email: EmailStr
14 |     first_name: str
15 |     last_name: str
16 |     user_image: str | None
17 |     settings: dict
18 | 
19 |     class Config:
20 |         from_attributes = True
21 | 
22 |     @field_validator("user_image")
23 |     @classmethod
24 |     def add_image_host(cls, image_url: str | None) -> str:
25 |         if image_url:
26 |             if "/static/" in image_url and settings.ENVIRONMENT == "development":
27 |                 return settings.STATIC_HOST + image_url
28 |         return image_url
29 | 


--------------------------------------------------------------------------------
/src/authentication/services.py:
--------------------------------------------------------------------------------
 1 | import asyncio
 2 | import secrets
 3 | import string
 4 | 
 5 | from fastapi import status
 6 | from httpx import AsyncClient, Response
 7 | from sqlalchemy import and_, func, or_, select
 8 | from sqlalchemy.ext.asyncio import AsyncSession
 9 | 
10 | from src.models import User
11 | from src.utils import get_hashed_password, verify_password
12 | 
13 | 
14 | # Authenticate user based on username/email and password
15 | async def authenticate_user(db_session: AsyncSession, login_identifier: str, password: str) -> User | None:
16 |     # Introduce a small delay to mitigate user enumeration attacks
17 |     await asyncio.sleep(0.1)
18 | 
19 |     user: User | None = await get_user_by_login_identifier(db_session, login_identifier=login_identifier)
20 | 
21 |     if not user:
22 |         return None
23 | 
24 |     # if user is found check password
25 |     if not verify_password(plain_password=password, hashed_password=user.password):
26 |         return None
27 | 
28 |     return user
29 | 
30 | 
31 | async def get_user_by_login_identifier(db_session: AsyncSession, *, login_identifier: str) -> User | None:
32 |     query = select(User).where(or_(User.email == login_identifier, User.username == login_identifier))
33 |     result = await db_session.execute(query)
34 |     user: User | None = result.scalar_one_or_none()
35 | 
36 |     return user
37 | 
38 | 
39 | async def get_user_by_email(db_session: AsyncSession, *, email: str) -> User | None:
40 |     query = select(User).where(and_(User.email == email, User.is_deleted.is_(False)))
41 |     result = await db_session.execute(query)
42 |     user: User | None = result.scalar_one_or_none()
43 | 
44 |     return user
45 | 
46 | 
47 | async def create_user_from_google_credentials(db_session: AsyncSession, **kwargs) -> User:
48 |     # generate random password for google user and hash it
49 |     alphabet = string.ascii_letters + string.digits + string.punctuation
50 |     password = "".join(secrets.choice(alphabet) for _ in range(20))
51 |     hashed_password = get_hashed_password(password)
52 | 
53 |     user = User(
54 |         username=kwargs.get("email"),  # Using Google email as username
55 |         email=kwargs.get("email"),
56 |         first_name=kwargs.get("given_name"),
57 |         last_name=kwargs.get("family_name"),
58 |         user_image=kwargs.get("picture"),
59 |         password=hashed_password,
60 |     )
61 |     db_session.add(user)
62 |     await db_session.commit()
63 | 
64 |     return user
65 | 
66 | 
67 | # https://stackoverflow.com/questions/16501895/how-do-i-get-user-profile-using-google-access-token
68 | # Verify the auth token received by client after google signin
69 | async def verify_google_token(google_access_token: str) -> dict[str, str] | None:
70 |     google_url = f"https://www.googleapis.com/oauth2/v3/userinfo?access_token={google_access_token}"
71 | 
72 |     async with AsyncClient() as client:
73 |         response: Response = await client.get(google_url)
74 |         if response.status_code == status.HTTP_200_OK:
75 |             user_info: dict = response.json()
76 |         else:
77 |             return None
78 | 
79 |     # check that user_info contains email, given and family name
80 |     if {"email", "given_name", "family_name"}.issubset(set(user_info)):
81 |         return user_info
82 | 
83 |     return None
84 | 
85 | 
86 | async def update_user_last_login(db_session: AsyncSession, *, user: User) -> None:
87 |     user.last_login = func.now()
88 |     await db_session.commit()
89 | 


--------------------------------------------------------------------------------
/src/authentication/utils.py:
--------------------------------------------------------------------------------
 1 | from datetime import datetime, timedelta
 2 | from typing import Any
 3 | 
 4 | from jose import jwt
 5 | 
 6 | from src.config import settings
 7 | 
 8 | 
 9 | def create_access_token(subject: str | Any, expires_delta: int = None) -> str:
10 |     if expires_delta is not None:
11 |         expires_delta = datetime.utcnow() + expires_delta
12 |     else:
13 |         expires_delta = datetime.utcnow() + timedelta(minutes=settings.ACCESS_TOKEN_EXPIRE_MINUTES)
14 | 
15 |     to_encode = {"exp": expires_delta, "sub": str(subject)}
16 |     encoded_jwt = jwt.encode(to_encode, settings.JWT_ACCESS_SECRET_KEY, settings.ENCRYPTION_ALGORITHM)
17 |     return encoded_jwt
18 | 
19 | 
20 | def create_refresh_token(subject: str | Any, expires_delta: int = None) -> str:
21 |     if expires_delta is not None:
22 |         expires_delta = datetime.utcnow() + expires_delta
23 |     else:
24 |         expires_delta = datetime.utcnow() + timedelta(minutes=settings.REFRESH_TOKEN_EXPIRE_MINUTES)
25 | 
26 |     to_encode = {"exp": expires_delta, "sub": str(subject)}
27 |     encoded_jwt = jwt.encode(to_encode, settings.JWT_REFRESH_SECRET_KEY, settings.ENCRYPTION_ALGORITHM)
28 |     return encoded_jwt
29 | 


--------------------------------------------------------------------------------
/src/chat/router.py:
--------------------------------------------------------------------------------
  1 | import json
  2 | import logging
  3 | from typing import Annotated
  4 | from uuid import UUID
  5 | 
  6 | import redis.asyncio as aioredis
  7 | from fastapi import APIRouter, Depends, HTTPException, Query, status
  8 | from sqlalchemy.ext.asyncio import AsyncSession
  9 | 
 10 | from src.chat.schemas import (
 11 |     CreateDirectChatSchema,
 12 |     DisplayDirectChatSchema,
 13 |     GetDirectChatSchema,
 14 |     GetDirectChatsSchema,
 15 |     GetMessagesSchema,
 16 |     GetOldMessagesSchema,
 17 | )
 18 | from src.chat.services import (
 19 |     create_direct_chat,
 20 |     direct_chat_exists,
 21 |     get_active_message_by_guid_and_chat,
 22 |     get_chat_by_guid,
 23 |     get_chat_messages,
 24 |     get_new_messages_per_chat,
 25 |     get_older_chat_messages,
 26 |     get_unread_messages_count,
 27 |     get_user_by_guid,
 28 |     get_user_direct_chats,
 29 | )
 30 | from src.config import settings
 31 | from src.database import get_async_session
 32 | from src.dependencies import get_cache, get_cache_setting, get_current_user
 33 | from src.models import Chat, Message, User
 34 | from src.utils import clear_cache_for_get_direct_chats
 35 | 
 36 | chat_router = APIRouter(tags=["Chat Management"])
 37 | 
 38 | logger = logging.getLogger(__name__)
 39 | 
 40 | 
 41 | @chat_router.post("/chat/direct/", summary="Create a direct chat", response_model=DisplayDirectChatSchema)
 42 | async def create_direct_chat_view(
 43 |     create_direct_chat_schema: CreateDirectChatSchema,
 44 |     db_session: AsyncSession = Depends(get_async_session),
 45 |     current_user: User = Depends(get_current_user),
 46 | ):
 47 |     # check if another user (recipient) exists
 48 |     recipient_user_guid = create_direct_chat_schema.recipient_user_guid
 49 |     recipient_user: User | None = await get_user_by_guid(db_session, user_guid=recipient_user_guid)
 50 | 
 51 |     # TODO: must check that recipient user is not the same as initiator
 52 |     if not recipient_user:
 53 |         raise HTTPException(
 54 |             status_code=status.HTTP_404_NOT_FOUND,
 55 |             detail=f"There is no recipient user with provided guid [{recipient_user_guid}]",
 56 |         )
 57 | 
 58 |     if await direct_chat_exists(db_session, current_user=current_user, recipient_user=recipient_user):
 59 |         raise HTTPException(
 60 |             status_code=status.HTTP_409_CONFLICT, detail=f"Chat with recipient user exists [{recipient_user_guid}]"
 61 |         )
 62 | 
 63 |     # Check if the data is already in the cache
 64 |     chat: Chat = await create_direct_chat(db_session, initiator_user=current_user, recipient_user=recipient_user)
 65 | 
 66 |     return chat
 67 | 
 68 | 
 69 | @chat_router.get("/chat/{chat_guid}/messages/", summary="Get user's chat messages")
 70 | async def get_user_messages_in_chat(
 71 |     chat_guid: UUID,
 72 |     size: Annotated[int | None, Query(gt=0, lt=200)] = 20,
 73 |     db_session: AsyncSession = Depends(get_async_session),
 74 |     current_user: User = Depends(get_current_user),
 75 |     cache: aioredis.Redis = Depends(get_cache),
 76 |     cache_enabled: bool = Depends(get_cache_setting),
 77 | ):
 78 |     chat: Chat | None = await get_chat_by_guid(db_session, chat_guid=chat_guid)
 79 | 
 80 |     if not chat:
 81 |         raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Chat with provided guid does not exist")
 82 | 
 83 |     # determine the number of unread messages
 84 |     unread_messages_count: int = await get_unread_messages_count(db_session, user_id=current_user.id, chat=chat)
 85 | 
 86 |     # get larger of provided messages size or unread messages
 87 |     size: int = max(size, unread_messages_count)
 88 | 
 89 |     # determine cache key
 90 |     cache_key: str = f"messages_{chat_guid}_{size}"
 91 | 
 92 |     if cache_enabled:
 93 |         # return cached chat messages if key exists
 94 |         if cached_chat_messages := await cache.get(cache_key):
 95 |             logger.info("Cache: Messages")
 96 |             return json.loads(cached_chat_messages)
 97 | 
 98 |     if current_user not in chat.users:
 99 |         raise HTTPException(status_code=status.HTTP_409_CONFLICT, detail="You don't have access to this chat")
100 | 
101 |     messages, has_more_messages, last_read_message = await get_chat_messages(
102 |         db_session, user_id=current_user.id, chat=chat, size=size
103 |     )
104 |     response = GetMessagesSchema(
105 |         messages=messages,
106 |         has_more_messages=has_more_messages,
107 |     )
108 |     if last_read_message:
109 |         response.last_read_message = last_read_message
110 | 
111 |     if cache_enabled:
112 |         # Store the chat in the cache with a TTL
113 |         await cache.set(cache_key, response.model_dump_json(), ex=settings.REDIS_CACHE_EXPIRATION_SECONDS)
114 | 
115 |     return response
116 | 
117 | 
118 | # TODO: when to clear the cache?
119 | @chat_router.get("/chats/direct/", summary="Get user's direct chats")
120 | async def get_user_chats_view(
121 |     db_session: AsyncSession = Depends(get_async_session),
122 |     current_user: User = Depends(get_current_user),
123 |     cache: aioredis.Redis = Depends(get_cache),
124 |     cache_enabled: bool = Depends(get_cache_setting),
125 | ):
126 |     # return cached direct chats if key exists
127 |     cache_key = f"direct_chats_{current_user.guid}"
128 |     if cached_direct_chats := await cache.get(cache_key):
129 |         logger.info("Cache: Chats")
130 |         return json.loads(cached_direct_chats)
131 | 
132 |     chats: list[Chat] = await get_user_direct_chats(db_session, current_user=current_user)
133 | 
134 |     chats_with_new_messages_count: list[GetDirectChatSchema] = await get_new_messages_per_chat(
135 |         db_session, chats, current_user
136 |     )
137 | 
138 |     # calculate total unread messages count for all user's chats
139 |     total_unread_messages_count = sum(direct_chat.new_messages_count for direct_chat in chats_with_new_messages_count)
140 | 
141 |     response = GetDirectChatsSchema(
142 |         chats=chats_with_new_messages_count, total_unread_messages_count=total_unread_messages_count
143 |     )
144 | 
145 |     if cache_enabled:
146 |         # Store response in the cache with a TTL
147 |         await cache.set(cache_key, response.model_dump_json(), ex=settings.REDIS_CACHE_EXPIRATION_SECONDS)
148 | 
149 |     return response
150 | 
151 | 
152 | @chat_router.delete("/chats/direct/{chat_guid}/", summary="Delete user's chat", status_code=status.HTTP_204_NO_CONTENT)
153 | async def delete_direct_chat_view(
154 |     chat_guid: UUID,
155 |     db_session: AsyncSession = Depends(get_async_session),
156 |     current_user: User = Depends(get_current_user),
157 |     cache: aioredis.Redis = Depends(get_cache),
158 |     cache_enabled: bool = Depends(get_cache_setting),
159 | ):
160 |     chat: Chat | None = await get_chat_by_guid(db_session, chat_guid=chat_guid)
161 | 
162 |     if not chat or current_user not in chat.users:
163 |         raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Chat with provided guid is not found")
164 | 
165 |     await db_session.delete(chat)
166 |     await db_session.commit()
167 | 
168 |     if cache_enabled:
169 |         for user in chat.users:
170 |             await clear_cache_for_get_direct_chats(cache=cache, user=user)
171 | 
172 | 
173 | @chat_router.get(
174 |     "/chat/{chat_guid}/messages/old/{message_guid}/",
175 |     summary="Get user's historical chat messages",
176 | )
177 | async def get_older_messages(
178 |     chat_guid: UUID,
179 |     message_guid: UUID,
180 |     limit: Annotated[int | None, Query(gt=0, lt=200)] = 10,
181 |     db_session: AsyncSession = Depends(get_async_session),
182 |     current_user: User = Depends(get_current_user),
183 | ):
184 |     chat: Chat | None = await get_chat_by_guid(db_session, chat_guid=chat_guid)
185 | 
186 |     if not chat:
187 |         raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Chat with provided guid is not found")
188 | 
189 |     if current_user not in chat.users:
190 |         raise HTTPException(status_code=status.HTTP_409_CONFLICT, detail="You don't have access to this chat")
191 | 
192 |     message: Message | None = await get_active_message_by_guid_and_chat(
193 |         db_session, chat_id=chat.id, message_guid=message_guid
194 |     )
195 | 
196 |     if not message:
197 |         raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Message with provided guid is not found")
198 | 
199 |     old_messages, has_more_messages = await get_older_chat_messages(
200 |         db_session, chat=chat, user_id=current_user.id, created_at=message.created_at, limit=limit
201 |     )
202 |     return GetOldMessagesSchema(messages=old_messages, has_more_messages=has_more_messages)
203 | 


--------------------------------------------------------------------------------
/src/chat/schemas.py:
--------------------------------------------------------------------------------
 1 | from datetime import datetime
 2 | 
 3 | from pydantic import UUID4, BaseModel, field_validator
 4 | 
 5 | from src.config import settings
 6 | from src.models import ChatType
 7 | 
 8 | 
 9 | class ChatSchema(BaseModel):
10 |     guid: UUID4
11 |     chat_type: ChatType
12 | 
13 | 
14 | class CreateDirectChatSchema(BaseModel):
15 |     recipient_user_guid: UUID4
16 | 
17 | 
18 | class UserSchema(BaseModel):
19 |     guid: UUID4
20 |     first_name: str
21 |     last_name: str
22 |     username: str
23 |     user_image: str | None
24 | 
25 |     class Config:
26 |         from_attributes = True
27 | 
28 |     @field_validator("user_image")
29 |     @classmethod
30 |     def add_image_host(cls, image_url: str | None) -> str:
31 |         if image_url:
32 |             if "/static/" in image_url and settings.ENVIRONMENT == "development":
33 |                 return settings.STATIC_HOST + image_url
34 |         return image_url
35 | 
36 | 
37 | class MessageSchema(BaseModel):
38 |     guid: UUID4
39 |     content: str
40 |     created_at: datetime
41 |     user: UserSchema
42 |     chat: ChatSchema
43 |     is_read: bool | None = False
44 |     is_new: bool | None = True
45 | 
46 | 
47 | class DisplayDirectChatSchema(BaseModel):
48 |     guid: UUID4
49 |     chat_type: ChatType
50 |     created_at: datetime
51 |     updated_at: datetime
52 |     users: list[UserSchema]
53 | 
54 | 
55 | class GetDirectChatSchema(BaseModel):
56 |     chat_guid: UUID4
57 |     chat_type: ChatType
58 |     created_at: datetime
59 |     updated_at: datetime
60 |     users: list[UserSchema]
61 |     new_messages_count: int
62 | 
63 |     class Config:
64 |         from_attributes = True
65 | 
66 | 
67 | class GetDirectChatsSchema(BaseModel):
68 |     chats: list[GetDirectChatSchema]
69 |     total_unread_messages_count: int
70 | 
71 | 
72 | class LastReadMessageSchema(BaseModel):
73 |     guid: UUID4
74 |     content: str
75 |     created_at: datetime
76 | 
77 | 
78 | class GetMessageSchema(BaseModel):
79 |     message_guid: UUID4
80 |     user_guid: UUID4
81 |     chat_guid: UUID4
82 |     content: str
83 |     created_at: datetime
84 |     is_read: bool | None = False
85 | 
86 | 
87 | class GetMessagesSchema(BaseModel):
88 |     messages: list[GetMessageSchema]
89 |     has_more_messages: bool
90 |     last_read_message: LastReadMessageSchema = None
91 | 
92 | 
93 | class GetOldMessagesSchema(BaseModel):
94 |     messages: list[GetMessageSchema]
95 |     has_more_messages: bool
96 | 


--------------------------------------------------------------------------------
/src/chat/services.py:
--------------------------------------------------------------------------------
  1 | import logging
  2 | from datetime import datetime
  3 | from uuid import UUID
  4 | 
  5 | from sqlalchemy import and_, func, select
  6 | from sqlalchemy.ext.asyncio import AsyncSession
  7 | from sqlalchemy.orm import aliased, selectinload
  8 | 
  9 | from src.chat.schemas import GetDirectChatSchema, GetMessageSchema
 10 | from src.models import Chat, ChatType, Message, ReadStatus, User
 11 | 
 12 | logger = logging.getLogger(__name__)
 13 | 
 14 | 
 15 | async def create_direct_chat(db_session: AsyncSession, *, initiator_user: User, recipient_user: User) -> Chat:
 16 |     try:
 17 |         chat = Chat(chat_type=ChatType.DIRECT)
 18 |         chat.users.append(initiator_user)
 19 |         chat.users.append(recipient_user)
 20 |         db_session.add(chat)
 21 |         await db_session.flush()
 22 | 
 23 |         # make empty read statuses for both users last_read_message_id = 0
 24 |         initiator_read_status = ReadStatus(chat_id=chat.id, user_id=initiator_user.id, last_read_message_id=0)
 25 |         recipient_read_status = ReadStatus(chat_id=chat.id, user_id=recipient_user.id, last_read_message_id=0)
 26 |         db_session.add_all([initiator_read_status, recipient_read_status])
 27 |         await db_session.commit()
 28 | 
 29 |     except Exception as exc_info:
 30 |         await db_session.rollback()
 31 |         raise exc_info
 32 | 
 33 |     else:
 34 |         return chat
 35 | 
 36 | 
 37 | async def get_direct_chat_by_users(
 38 |     db_session: AsyncSession, *, initiator_user: User, recipient_user: User
 39 | ) -> Chat | None:
 40 |     query = select(Chat).where(
 41 |         and_(
 42 |             Chat.chat_type == ChatType.DIRECT, Chat.users.contains(initiator_user), Chat.users.contains(recipient_user)
 43 |         )
 44 |     )
 45 | 
 46 |     result = await db_session.execute(query)
 47 |     chat: Chat | None = result.scalar_one_or_none()
 48 | 
 49 |     return chat
 50 | 
 51 | 
 52 | async def get_chat_by_guid(db_session: AsyncSession, *, chat_guid: UUID) -> Chat | None:
 53 |     query = (
 54 |         select(Chat)
 55 |         .where(Chat.guid == chat_guid)
 56 |         .options(selectinload(Chat.messages), selectinload(Chat.users), selectinload(Chat.read_statuses))
 57 |     )
 58 |     result = await db_session.execute(query)
 59 |     chat: Chat | None = result.scalar_one_or_none()
 60 | 
 61 |     return chat
 62 | 
 63 | 
 64 | async def get_user_by_guid(db_session: AsyncSession, *, user_guid: UUID) -> User | None:
 65 |     query = select(User).where(User.guid == user_guid)
 66 |     result = await db_session.execute(query)
 67 |     user: User | None = result.scalar_one_or_none()
 68 | 
 69 |     return user
 70 | 
 71 | 
 72 | async def get_new_messages_per_chat(
 73 |     db_session: AsyncSession, chats: list[Chat], current_user: User
 74 | ) -> list[GetDirectChatSchema]:
 75 |     """
 76 |     New message are those messages that:
 77 |     - don't belong to current user
 78 |     - are not yet read by current user
 79 | 
 80 |     """
 81 |     # Create a dictionary with default values of 0
 82 |     new_messages_count_per_chat = {chat.id: 0 for chat in chats}
 83 | 
 84 |     # Create an alias for the ReadStatus table
 85 |     read_status_alias = aliased(ReadStatus)
 86 | 
 87 |     query = (
 88 |         select(Message.chat_id, func.count().label("message_count"))
 89 |         .join(
 90 |             read_status_alias,
 91 |             and_(read_status_alias.user_id == current_user.id, read_status_alias.chat_id == Message.chat_id),
 92 |         )
 93 |         .where(
 94 |             and_(
 95 |                 Message.user_id != current_user.id,
 96 |                 Message.id > func.coalesce(read_status_alias.last_read_message_id, 0),
 97 |                 Message.is_deleted.is_(False),
 98 |                 Message.chat_id.in_(new_messages_count_per_chat),
 99 |             )
100 |         )
101 |         .group_by(Message.chat_id)
102 |     )
103 | 
104 |     result = await db_session.execute(query)
105 |     new_messages_count = result.fetchall()
106 | 
107 |     for messages_count in new_messages_count:
108 |         new_messages_count_per_chat[messages_count[0]] = messages_count[1]
109 | 
110 |     return [
111 |         GetDirectChatSchema(
112 |             chat_guid=chat.guid,
113 |             chat_type=chat.chat_type,
114 |             created_at=chat.created_at,
115 |             updated_at=chat.updated_at,
116 |             users=chat.users,
117 |             new_messages_count=new_messages_count_per_chat[chat.id],
118 |         )
119 |         for chat in chats
120 |     ]
121 | 
122 | 
123 | async def get_user_direct_chats(db_session: AsyncSession, *, current_user: User) -> list[Chat]:
124 |     query = (
125 |         select(Chat)
126 |         .where(
127 |             and_(
128 |                 Chat.users.contains(current_user),
129 |                 Chat.is_deleted.is_(False),
130 |                 Chat.chat_type == ChatType.DIRECT,
131 |             )
132 |         )
133 |         .options(selectinload(Chat.users))
134 |     ).order_by(Chat.updated_at.desc())
135 |     result = await db_session.execute(query)
136 | 
137 |     chats: list[Chat] = result.scalars().all()
138 | 
139 |     return chats
140 | 
141 | 
142 | async def direct_chat_exists(db_session: AsyncSession, *, current_user: User, recipient_user: User) -> bool:
143 |     query = select(Chat.id).where(
144 |         and_(
145 |             Chat.chat_type == ChatType.DIRECT,
146 |             Chat.is_deleted.is_(False),
147 |             Chat.users.contains(current_user),
148 |             Chat.users.contains(recipient_user),
149 |         )
150 |     )
151 |     result = await db_session.execute(query)
152 |     existing_chat = result.scalar_one_or_none()
153 |     return existing_chat is not None
154 | 
155 | 
156 | async def get_chat_messages(
157 |     db_session: AsyncSession, *, user_id: int, chat: Chat, size: int
158 | ) -> tuple[list[Chat], bool, Message | None]:
159 |     query = (
160 |         select(Message)
161 |         .where(and_(Message.chat_id == chat.id, Message.is_deleted.is_(False)))
162 |         .order_by(Message.created_at.desc())
163 |         .limit(size + 1)
164 |         .options(selectinload(Message.user), selectinload(Message.chat))
165 |     )
166 |     result = await db_session.execute(query)
167 |     messages: list[Message] = result.scalars().all()
168 |     # check if there are more messages
169 |     has_more_messages = len(messages) > size
170 |     messages = messages[:size]
171 | 
172 |     # assuming only two read statuses
173 |     # Initialize variables to prevent NameError
174 |     my_last_read_message_id = None
175 |     other_user_last_read_message_id = None
176 | 
177 |     # Loop through chat.read_statuses and assign the read message IDs
178 |     for read_status in chat.read_statuses:
179 |         if read_status.user_id != user_id:
180 |             other_user_last_read_message_id = read_status.last_read_message_id
181 |         else:
182 |             my_last_read_message_id = read_status.last_read_message_id
183 | 
184 |     # If no value is assigned, you could choose to set a default value
185 |     # or handle this case accordingly (e.g., using a placeholder)
186 |     if my_last_read_message_id is None:
187 |         my_last_read_message_id = 0  # Or some other default value
188 | 
189 |     if other_user_last_read_message_id is None:
190 |         other_user_last_read_message_id = 0  # Or some other default value
191 | 
192 |     # Retrieve the last read message for the other user
193 |     last_read_message = await db_session.get(Message, other_user_last_read_message_id)
194 | 
195 |     # Construct GetMessageSchema list
196 |     get_message_schemas = [
197 |         GetMessageSchema(
198 |             message_guid=message.guid,
199 |             content=message.content,
200 |             created_at=message.created_at,
201 |             chat_guid=message.chat.guid,
202 |             user_guid=message.user.guid,
203 |             is_read=message.id
204 |             <= (other_user_last_read_message_id if message.user.id == user_id else my_last_read_message_id),
205 |         )
206 |         for message in messages
207 |     ]
208 | 
209 |     return get_message_schemas, has_more_messages, last_read_message
210 | 
211 | 
212 | async def get_active_message_by_guid_and_chat(
213 |     db_session: AsyncSession, *, chat_id: int, message_guid: UUID
214 | ) -> Message | None:
215 |     query = select(Message).where(
216 |         and_(Message.guid == message_guid, Message.is_deleted.is_(False), Message.chat_id == chat_id)
217 |     )
218 | 
219 |     result = await db_session.execute(query)
220 |     message: Message | None = result.scalar_one_or_none()
221 | 
222 |     return message
223 | 
224 | 
225 | async def get_older_chat_messages(
226 |     db_session: AsyncSession,
227 |     *,
228 |     chat: Chat,
229 |     user_id: int,
230 |     limit: int = 10,
231 |     created_at: datetime,
232 | ) -> tuple[list[Message], bool]:
233 |     query = (
234 |         select(Message)
235 |         .where(
236 |             and_(
237 |                 Message.chat_id == chat.id,
238 |                 Message.is_deleted.is_(False),
239 |                 Message.created_at < created_at,
240 |             )
241 |         )
242 |         .order_by(Message.created_at.desc())
243 |         .limit(limit + 1)  # Fetch limit + 1 messages
244 |         .options(selectinload(Message.user), selectinload(Message.chat))
245 |     )
246 | 
247 |     result = await db_session.execute(query)
248 |     older_messages: list[Message] = result.scalars().all()
249 | 
250 |     # Determine if there are more messages
251 |     has_more_messages = len(older_messages) > limit
252 |     older_messages = older_messages[:limit]
253 | 
254 |     # assuming only two read statuses
255 |     for read_status in chat.read_statuses:
256 |         if read_status.user_id != user_id:
257 |             other_user_last_read_message_id = read_status.last_read_message_id
258 |         else:
259 |             my_last_read_message_id = read_status.last_read_message_id
260 | 
261 |     get_message_schemas = [
262 |         GetMessageSchema(
263 |             message_guid=message.guid,
264 |             content=message.content,
265 |             created_at=message.created_at,
266 |             chat_guid=message.chat.guid,
267 |             user_guid=message.user.guid,
268 |             is_read=message.id
269 |             <= (other_user_last_read_message_id if message.user.id == user_id else my_last_read_message_id),
270 |         )
271 |         for message in older_messages
272 |     ]
273 | 
274 |     # Return the first 'limit' messages and a flag indicating if there are more
275 |     return get_message_schemas, has_more_messages
276 | 
277 | 
278 | async def add_new_messages_stats_to_direct_chat(
279 |     db_session: AsyncSession, *, current_user: User, chat: Chat
280 | ) -> GetDirectChatSchema:
281 |     # new non-model (chat) fields are added
282 |     has_new_messages: bool = False
283 |     new_messages_count: int
284 | 
285 |     # assuming chat has two read statuses
286 |     # current user's read status is used to determine new messages count
287 |     for read_status in chat.read_statuses:
288 |         # own read status -> for new messages
289 |         if read_status.user_id == current_user.id:
290 |             my_last_read_message_id = read_status.last_read_message_id
291 | 
292 |     new_messages_query = select(func.count()).where(
293 |         and_(
294 |             Message.user_id != current_user.id,
295 |             Message.id > my_last_read_message_id,
296 |             Message.is_deleted.is_(False),
297 |             Message.chat_id == chat.id,
298 |         )
299 |     )
300 |     result = await db_session.execute(new_messages_query)
301 |     new_messages_count: int = result.scalar_one_or_none()
302 |     if new_messages_count:
303 |         has_new_messages = True
304 | 
305 |     return GetDirectChatSchema(
306 |         chat_guid=chat.guid,
307 |         chat_type=chat.chat_type,
308 |         created_at=chat.created_at,
309 |         updated_at=chat.updated_at,
310 |         users=chat.users,
311 |         has_new_messages=has_new_messages,
312 |         new_messages_count=new_messages_count,
313 |     )
314 | 
315 | 
316 | async def get_unread_messages_count(db_session: AsyncSession, *, user_id: int, chat: Chat) -> int:
317 |     # Get the user's last read message ID in the chat
318 |     user_read_status = next((rs for rs in chat.read_statuses if rs.user_id == user_id), None)
319 |     if not user_read_status:
320 |         return 0  # User has no read status in this chat
321 | 
322 |     user_last_read_message_id = user_read_status.last_read_message_id
323 | 
324 |     # Count the number of unread messages for the user
325 |     query = select(func.count()).where(
326 |         and_(
327 |             Message.chat_id == chat.id,
328 |             Message.is_deleted.is_(False),
329 |             Message.id > user_last_read_message_id,
330 |         )
331 |     )
332 | 
333 |     result = await db_session.execute(query)
334 |     unread_messages_count = result.scalar()
335 | 
336 |     return unread_messages_count
337 | 


--------------------------------------------------------------------------------
/src/config.py:
--------------------------------------------------------------------------------
  1 | import logging
  2 | import os
  3 | from random import randint
  4 | 
  5 | import boto3
  6 | from pydantic_settings import BaseSettings, SettingsConfigDict
  7 | 
  8 | 
  9 | class GlobalSettings(BaseSettings):
 10 |     model_config = SettingsConfigDict(env_file=".env", env_file_encoding="utf-8")
 11 | 
 12 |     ENVIRONMENT: str = "development"
 13 |     # app settings
 14 |     ALLOWED_ORIGINS: str = "http://127.0.0.1:3000,http://localhost:3000"
 15 | 
 16 |     # Logging
 17 |     LOG_LEVEL: int = logging.DEBUG
 18 | 
 19 |     # Sentry
 20 |     SENTRY_DSN: str = ""
 21 | 
 22 |     DB_USER: str = "postgres"
 23 |     DB_PASSWORD: str = "postgres"
 24 |     DB_HOST: str = "chat-postgres"
 25 |     DB_PORT: str = "5432"
 26 |     DB_NAME: str = "postgres"
 27 |     DB_SCHEMA: str = "chat"
 28 |     # specify single database url
 29 |     DATABASE_URL: str | None = None
 30 | 
 31 |     # authentication related
 32 |     JWT_ACCESS_SECRET_KEY: str = "9d9bc4d77ac3a6fce1869ec8222729d2"
 33 |     JWT_REFRESH_SECRET_KEY: str = "fdc5635260b464a0b8e12835800c9016"
 34 |     ENCRYPTION_ALGORITHM: str = "HS256"
 35 |     ACCESS_TOKEN_EXPIRE_MINUTES: int = 30
 36 |     NEW_ACCESS_TOKEN_EXPIRE_MINUTES: int = 120
 37 |     REFRESH_TOKEN_EXPIRE_MINUTES: int = 60 * 24
 38 | 
 39 |     # admin
 40 |     ADMIN_SECRET_KEY: str = "Hv9LGqARc473ceBUYDw1FR0QaXOA3Ky4"
 41 | 
 42 |     # redis for caching
 43 |     REDIS_CACHE_ENABLED: bool = True
 44 |     REDIS_HOST: str = "chat-redis"
 45 |     REDIS_PORT: str | int = 6379
 46 |     REDIS_PASSWORD: str | None = None
 47 |     REDIS_CACHE_EXPIRATION_SECONDS: int = 60 * 30
 48 |     REDIS_DB: int = 0
 49 | 
 50 |     # websocket
 51 |     # user status
 52 |     SECONDS_TO_SEND_USER_STATUS: int = 60
 53 | 
 54 |     # admin
 55 |     ADMIN_SECRET_KEY: str = "Hs9LGqARc909ceBUYDw2Fs0QaXOA3Ky4"
 56 | 
 57 |     # static files
 58 |     STATIC_HOST: str = "http://localhost:8001"
 59 | 
 60 | 
 61 | class TestSettings(GlobalSettings):
 62 |     DB_SCHEMA: str = f"test_{randint(1, 100)}"
 63 | 
 64 | 
 65 | class DevelopmentSettings(GlobalSettings):
 66 |     pass
 67 | 
 68 | 
 69 | class ProductionSettings(GlobalSettings):
 70 |     AWS_ACCESS_KEY_ID: str
 71 |     AWS_SECRET_ACCESS_KEY: str
 72 |     AWS_REGION_NAME: str
 73 |     AWS_IMAGES_BUCKET: str
 74 | 
 75 |     LOG_LEVEL: int = logging.INFO
 76 | 
 77 |     @staticmethod
 78 |     def get_aws_client_for_image_upload():
 79 |         if all(
 80 |             (
 81 |                 aws_access_key_id := os.environ.get("AWS_ACCESS_KEY_ID"),
 82 |                 aws_secret_access_key := os.environ.get("AWS_SECRET_ACCESS_KEY"),
 83 |                 region_name := os.environ.get("AWS_REGION_NAME", ""),
 84 |             )
 85 |         ):
 86 |             aws_session = boto3.Session(
 87 |                 aws_access_key_id=aws_access_key_id,
 88 |                 aws_secret_access_key=aws_secret_access_key,
 89 |                 region_name=region_name,
 90 |             )
 91 |             s3_resource = aws_session.resource("s3")
 92 | 
 93 |             return s3_resource.meta.client
 94 |         else:
 95 |             return None
 96 | 
 97 | 
 98 | def get_settings():
 99 |     env = os.environ.get("ENVIRONMENT", "development")
100 |     if env == "test":
101 |         return TestSettings()
102 |     elif env == "development":
103 |         return DevelopmentSettings()
104 |     elif env == "production":
105 |         return ProductionSettings()
106 | 
107 |     return GlobalSettings()
108 | 
109 | 
110 | settings = get_settings()
111 | 
112 | 
113 | LOGGING_CONFIG: dict = {
114 |     "version": 1,
115 |     "disable_existing_loggers": False,
116 |     "formatters": {
117 |         "standard": {"format": "%(asctime)s [%(levelname)s] %(name)s: %(message)s"},
118 |     },
119 |     "handlers": {
120 |         "default": {
121 |             "level": settings.LOG_LEVEL,
122 |             "formatter": "standard",
123 |             "class": "logging.StreamHandler",
124 |             "stream": "ext://sys.stdout",  # Default is stderr
125 |         },
126 |     },
127 |     "loggers": {
128 |         "": {"handlers": ["default"], "level": settings.LOG_LEVEL, "propagate": False},
129 |         "uvicorn": {
130 |             "handlers": ["default"],
131 |             "level": logging.ERROR,
132 |             "propagate": False,
133 |         },
134 |     },
135 | }
136 | 


--------------------------------------------------------------------------------
/src/contact/router.py:
--------------------------------------------------------------------------------
 1 | import json
 2 | import logging
 3 | 
 4 | import redis.asyncio as aioredis
 5 | from fastapi import APIRouter, Depends
 6 | from sqlalchemy.ext.asyncio import AsyncSession
 7 | 
 8 | from src.config import settings
 9 | from src.contact.schemas import GetUsersResponseSchema
10 | from src.contact.services import get_all_users
11 | from src.database import get_async_session
12 | from src.dependencies import get_cache, get_cache_setting, get_current_user
13 | from src.models import User
14 | 
15 | logger = logging.getLogger(__name__)
16 | 
17 | contact_router = APIRouter(tags=["Contact Management"])
18 | 
19 | 
20 | @contact_router.get("/users/", summary="Get all users")
21 | async def get_contacts_view(
22 |     db_session: AsyncSession = Depends(get_async_session),
23 |     current_user: User = Depends(get_current_user),
24 |     cache: aioredis.Redis = Depends(get_cache),
25 |     cache_enabled: bool = Depends(get_cache_setting),
26 | ):
27 |     cache_key = f"{current_user.guid}_all_users"
28 | 
29 |     if cache_enabled:
30 |         # return cached users list if key exists
31 |         if cached_all_users := await cache.get(cache_key):
32 |             logging.info("Cache: Users")
33 |             return json.loads(cached_all_users)
34 | 
35 |     users: list[User] = await get_all_users(db_session, current_user=current_user)
36 | 
37 |     response = GetUsersResponseSchema.model_validate(users, from_attributes=True)
38 | 
39 |     if cache_enabled:
40 |         await cache.set(cache_key, response.model_dump_json(), ex=settings.REDIS_CACHE_EXPIRATION_SECONDS)
41 | 
42 |     return response
43 | 


--------------------------------------------------------------------------------
/src/contact/schemas.py:
--------------------------------------------------------------------------------
 1 | from datetime import datetime
 2 | 
 3 | from pydantic import UUID4, BaseModel, RootModel, field_validator
 4 | 
 5 | from src.config import settings
 6 | 
 7 | 
 8 | class GetUserSchema(BaseModel):
 9 |     guid: UUID4
10 |     username: str
11 |     email: str
12 |     first_name: str
13 |     last_name: str
14 |     created_at: datetime
15 |     user_image: str | None
16 | 
17 |     @field_validator("user_image")
18 |     @classmethod
19 |     def add_image_host(cls, image_url: str | None) -> str:
20 |         if image_url:
21 |             if "/static/" in image_url and settings.ENVIRONMENT == "development":
22 |                 return settings.STATIC_HOST + image_url
23 |         return image_url
24 | 
25 | 
26 | class GetUsersResponseSchema(RootModel[GetUserSchema]):
27 |     root: list[GetUserSchema]
28 | 


--------------------------------------------------------------------------------
/src/contact/services.py:
--------------------------------------------------------------------------------
 1 | from sqlalchemy import and_, not_, select
 2 | from sqlalchemy.ext.asyncio import AsyncSession
 3 | 
 4 | from src.contact.schemas import GetUserSchema
 5 | from src.models import User
 6 | 
 7 | 
 8 | async def get_all_users(db_session: AsyncSession, *, current_user: User) -> list[GetUserSchema]:
 9 |     query = (
10 |         select(User).where(
11 |             and_(
12 |                 not_(User.id == current_user.id),
13 |                 User.is_deleted.is_(False),
14 |             )
15 |         )
16 |     ).order_by(User.username)
17 |     result = await db_session.execute(query)
18 | 
19 |     users: list[User] = result.scalars().all()
20 | 
21 |     return users
22 | 


--------------------------------------------------------------------------------
/src/database.py:
--------------------------------------------------------------------------------
 1 | from datetime import datetime
 2 | from typing import AsyncGenerator
 3 | 
 4 | import redis.asyncio as aioredis
 5 | from sqlalchemy import Boolean, DateTime, MetaData, func
 6 | from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine
 7 | from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, sessionmaker
 8 | 
 9 | from src.config import settings
10 | 
11 | DATABASE_URL = settings.DATABASE_URL or (
12 |     f"postgresql+asyncpg://"
13 |     f"{settings.DB_USER}:{settings.DB_PASSWORD}@{settings.DB_HOST}:{settings.DB_PORT}/{settings.DB_NAME}"
14 | )
15 | 
16 | 
17 | metadata = MetaData(schema=settings.DB_SCHEMA)
18 | 
19 | 
20 | class RemoveBaseFieldsMixin:
21 |     created_at = None
22 |     updated_at = None
23 |     is_deleted = None
24 | 
25 | 
26 | class BaseModel(DeclarativeBase):
27 |     __abstract__ = True
28 |     # to not declare schema each time table is created
29 |     metadata = metadata
30 | 
31 |     created_at: Mapped[datetime] = mapped_column(DateTime(timezone=True), server_default=func.now())
32 |     updated_at: Mapped[datetime] = mapped_column(
33 |         DateTime(timezone=True), server_default=func.now(), onupdate=func.now()
34 |     )
35 |     is_deleted: Mapped[bool] = mapped_column(Boolean, default=False)
36 | 
37 |     def to_dict(self):
38 |         return {field.name: getattr(self, field.name) for field in self.__table__.c}
39 | 
40 | 
41 | engine = create_async_engine(
42 |     DATABASE_URL, pool_size=40, max_overflow=20, pool_recycle=3600, isolation_level="AUTOCOMMIT"
43 | )
44 | async_session_maker = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)
45 | 
46 | 
47 | async def get_async_session() -> AsyncGenerator[AsyncSession, None]:
48 |     async with async_session_maker() as session:
49 |         yield session
50 | 
51 | 
52 | def create_redis_pool():
53 |     return aioredis.ConnectionPool(
54 |         host=settings.REDIS_HOST, port=settings.REDIS_PORT, password=settings.REDIS_PASSWORD, db=settings.REDIS_DB
55 |     )
56 | 
57 | 
58 | redis_pool = create_redis_pool()
59 | 


--------------------------------------------------------------------------------
/src/dependencies.py:
--------------------------------------------------------------------------------
 1 | from typing import Annotated
 2 | 
 3 | import jwt
 4 | import redis.asyncio as aioredis
 5 | from fastapi import Cookie, Depends, HTTPException, status
 6 | from sqlalchemy.ext.asyncio import AsyncSession
 7 | 
 8 | from src.authentication.services import get_user_by_login_identifier
 9 | from src.config import settings
10 | from src.database import get_async_session, redis_pool
11 | from src.models import User
12 | 
13 | 
14 | async def get_current_user(
15 |     access_token: Annotated[str | None, Cookie()],
16 |     db_session: AsyncSession = Depends(get_async_session),
17 | ) -> User:
18 |     try:
19 |         payload = jwt.decode(access_token, settings.JWT_ACCESS_SECRET_KEY, algorithms=[settings.ENCRYPTION_ALGORITHM])
20 |         login_identifier: str = payload.get("sub")
21 |         if not login_identifier:
22 |             raise HTTPException(
23 |                 status_code=status.HTTP_401_UNAUTHORIZED,
24 |                 detail="Could not validate credentials",
25 |                 headers={"WWW-Authenticate": "Bearer"},
26 |             )
27 |     except jwt.ExpiredSignatureError:
28 |         raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Token expired")
29 |     except jwt.InvalidTokenError:
30 |         raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid Access Token")
31 |     except jwt.PyJWTError:
32 |         raise HTTPException(
33 |             status_code=status.HTTP_401_UNAUTHORIZED,
34 |             detail="Could not validate credentials",
35 |             headers={"WWW-Authenticate": "Bearer"},
36 |         )
37 | 
38 |     user: User | None = await get_user_by_login_identifier(db_session, login_identifier=login_identifier)
39 | 
40 |     if not user:
41 |         raise HTTPException(
42 |             status_code=status.HTTP_401_UNAUTHORIZED,
43 |             detail="Could not validate credentials",
44 |             headers={"WWW-Authenticate": "Bearer"},
45 |         )
46 | 
47 |     if user.is_deleted:
48 |         raise HTTPException(
49 |             status_code=status.HTTP_401_UNAUTHORIZED,
50 |             detail="User is not active",
51 |             headers={"WWW-Authenticate": "Bearer"},
52 |         )
53 | 
54 |     return user
55 | 
56 | 
57 | async def get_cache_setting():
58 |     return settings.REDIS_CACHE_ENABLED
59 | 
60 | 
61 | async def get_cache() -> aioredis.Redis:
62 |     return aioredis.Redis(connection_pool=redis_pool)
63 | 


--------------------------------------------------------------------------------
/src/main.py:
--------------------------------------------------------------------------------
 1 | import logging
 2 | 
 3 | import redis.asyncio as aioredis
 4 | import sentry_sdk
 5 | from fastapi import FastAPI
 6 | from fastapi.middleware.cors import CORSMiddleware
 7 | from fastapi.staticfiles import StaticFiles
 8 | from fastapi_limiter import FastAPILimiter
 9 | from fastapi_pagination import add_pagination
10 | from sqladmin import Admin
11 | 
12 | from src.admin.admin import admin_models
13 | from src.admin.authentication_backend import authentication_backend
14 | from src.config import LOGGING_CONFIG, settings
15 | from src.database import engine, redis_pool
16 | from src.routers import routers
17 | 
18 | # from sentry_sdk.integrations.asyncpg import AsyncPGIntegration
19 | 
20 | if not settings.ENVIRONMENT == "test":
21 |     logging.config.dictConfig(LOGGING_CONFIG)
22 |     sentry_sdk.init(
23 |         dsn=settings.SENTRY_DSN,
24 |         environment=settings.ENVIRONMENT,
25 |         # integrations=[
26 |         #     AsyncPGIntegration(),
27 |         # ],
28 |         enable_tracing=True,
29 |         # Set traces_sample_rate to 1.0 to capture 100%
30 |         # of transactions for performance monitoring.
31 |         traces_sample_rate=1.0,
32 |         # Set profiles_sample_rate to 1.0 to profile 100%
33 |         # of sampled transactions.
34 |         # We recommend adjusting this value in production.
35 |         profiles_sample_rate=1.0,
36 |     )
37 | 
38 | logger = logging.getLogger(__name__)
39 | 
40 | 
41 | app = FastAPI()
42 | app.mount("/static", StaticFiles(directory="src/static"), name="static")
43 | 
44 | admin = Admin(app, engine, authentication_backend=authentication_backend)
45 | 
46 | for router in routers:
47 |     app.include_router(router)
48 | 
49 | 
50 | allowed_origins = settings.ALLOWED_ORIGINS.split(",")
51 | 
52 | 
53 | app.add_middleware(
54 |     CORSMiddleware,
55 |     allow_origins=allowed_origins,
56 |     allow_credentials=True,
57 |     allow_methods=["*"],
58 |     allow_headers=["*"],
59 | )
60 | 
61 | add_pagination(app)
62 | 
63 | 
64 | @app.on_event("startup")
65 | async def startup():
66 |     logger.info("Application is started")
67 |     redis = aioredis.Redis(connection_pool=redis_pool)
68 |     await FastAPILimiter.init(redis)
69 | 
70 | 
71 | # Error displayed on shutdown (will be fixed in later versions): https://github.com/python/cpython/issues/109538
72 | @app.on_event("shutdown")
73 | async def shutdown_event():
74 |     logger.info("Application is closed")
75 | 
76 | 
77 | # register admin models
78 | for model in admin_models:
79 |     admin.add_view(model)
80 | 


--------------------------------------------------------------------------------
/src/managers/pubsub_manager.py:
--------------------------------------------------------------------------------
 1 | from uuid import UUID
 2 | 
 3 | import redis.asyncio as aioredis
 4 | 
 5 | from src.database import redis_pool
 6 | 
 7 | 
 8 | class RedisPubSubManager:
 9 |     def __init__(self):
10 |         self.pubsub = None
11 | 
12 |     async def _get_redis_connection(self) -> aioredis.Redis:
13 |         return aioredis.Redis(connection_pool=redis_pool)
14 | 
15 |     async def connect(self):
16 |         self.redis_connection = await self._get_redis_connection()
17 |         self.pubsub = self.redis_connection.pubsub()
18 | 
19 |     async def subscribe(self, chat_guid: UUID) -> aioredis.Redis:
20 |         await self.pubsub.subscribe(chat_guid)
21 |         return self.pubsub
22 | 
23 |     async def unsubscribe(self, chat_guid: UUID):
24 |         await self.pubsub.unsubscribe(chat_guid)
25 | 
26 |     async def publish(self, chat_guid: UUID, message: str):
27 |         await self.redis_connection.publish(chat_guid, message)
28 | 
29 |     async def disconnect(self):
30 |         await self.redis_connection.close()
31 | 


--------------------------------------------------------------------------------
/src/managers/websocket_manager.py:
--------------------------------------------------------------------------------
 1 | import asyncio
 2 | import json
 3 | import logging
 4 | 
 5 | from fastapi import WebSocket
 6 | 
 7 | from src.managers.pubsub_manager import RedisPubSubManager
 8 | 
 9 | logger = logging.getLogger(__name__)
10 | 
11 | 
12 | class WebSocketManager:
13 |     def __init__(self):
14 |         self.handlers: dict = {}
15 |         self.chats: dict = {}  # stores user WebSocket connections by chat {"chat_guid": {ws1, ws2}, ...}
16 |         self.pubsub_client = RedisPubSubManager()
17 |         self.user_guid_to_websocket: dict = {}  # stores user_guid: {ws1, ws2} combinations
18 | 
19 |     def handler(self, message_type):
20 |         def decorator(func):
21 |             self.handlers[message_type] = func
22 |             return func
23 | 
24 |         return decorator
25 | 
26 |     async def connect_socket(self, websocket: WebSocket):
27 |         await websocket.accept()
28 | 
29 |     async def add_user_socket_connection(self, user_guid: str, websocket: WebSocket):
30 |         self.user_guid_to_websocket.setdefault(user_guid, set()).add(websocket)
31 | 
32 |     async def add_user_to_chat(self, chat_guid: str, websocket: WebSocket):
33 |         if chat_guid in self.chats:
34 |             self.chats[chat_guid].add(websocket)
35 |         else:
36 |             self.chats[chat_guid] = {websocket}
37 |             await self.pubsub_client.connect()
38 |             pubsub_subscriber = await self.pubsub_client.subscribe(chat_guid)
39 |             asyncio.create_task(self._pubsub_data_reader(pubsub_subscriber))
40 | 
41 |     async def broadcast_to_chat(self, chat_guid: str, message: str | dict) -> None:
42 |         if isinstance(message, dict):
43 |             message = json.dumps(message)
44 |         await self.pubsub_client.publish(chat_guid, message)
45 | 
46 |     async def remove_user_from_chat(self, chat_guid: str, websocket: WebSocket) -> None:
47 |         self.chats[chat_guid].remove(websocket)
48 |         if len(self.chats[chat_guid]) == 0:
49 |             del self.chats[chat_guid]
50 |             logger.info("Removing user from PubSub channel {chat_guid}")
51 |             await self.pubsub_client.unsubscribe(chat_guid)
52 | 
53 |     async def remove_user_guid_to_websocket(self, user_guid: str, websocket: WebSocket):
54 |         if user_guid in self.user_guid_to_websocket:
55 |             self.user_guid_to_websocket.get(user_guid).remove(websocket)
56 | 
57 |     # https://github.com/redis/redis-py/issues/2523
58 |     async def _pubsub_data_reader(self, pubsub_subscriber):
59 |         try:
60 |             while True:
61 |                 message = await pubsub_subscriber.get_message(ignore_subscribe_messages=True)
62 |                 if message is not None:
63 |                     chat_guid = message["channel"].decode("utf-8")
64 |                     sockets = self.chats.get(chat_guid)
65 |                     if sockets:
66 |                         for socket in sockets:
67 |                             data = message["data"].decode("utf-8")
68 |                             await socket.send_text(data)
69 |         except Exception as exc:
70 |             logger.exception(f"Exception occurred: {exc}")
71 | 
72 |     async def send_error(self, message: str, websocket: WebSocket):
73 |         await websocket.send_json({"status": "error", "message": message})
74 | 


--------------------------------------------------------------------------------
/src/models.py:
--------------------------------------------------------------------------------
  1 | import enum
  2 | import uuid
  3 | from datetime import datetime
  4 | from typing import Any, List
  5 | 
  6 | from sqlalchemy import Boolean, Column, DateTime, Enum, ForeignKey, Index, String, Table
  7 | from sqlalchemy.dialects.postgresql import JSONB, UUID
  8 | from sqlalchemy.orm import Mapped, mapped_column, relationship
  9 | 
 10 | from src.database import BaseModel, RemoveBaseFieldsMixin, metadata
 11 | 
 12 | 
 13 | class ChatType(enum.Enum):
 14 |     DIRECT = "direct"
 15 |     GROUP = "group"
 16 | 
 17 | 
 18 | chat_participant = Table(
 19 |     "chat_participant",
 20 |     metadata,
 21 |     Column("user_id", ForeignKey("user.id"), primary_key=True),
 22 |     Column("chat_id", ForeignKey("chat.id"), primary_key=True),
 23 | )
 24 | 
 25 | 
 26 | class User(BaseModel):
 27 |     __tablename__ = "user"
 28 | 
 29 |     id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
 30 |     guid: Mapped[uuid.UUID] = mapped_column(UUID(as_uuid=True), unique=True, default=uuid.uuid4)
 31 |     password: Mapped[str] = mapped_column(String(128))
 32 |     username: Mapped[str] = mapped_column(String(150), unique=True)
 33 |     first_name: Mapped[str] = mapped_column(String(150), default="")
 34 |     last_name: Mapped[str] = mapped_column(String(150), default="")
 35 |     email: Mapped[str] = mapped_column(String(254), unique=True)
 36 |     last_login: Mapped[datetime] = mapped_column(DateTime(timezone=True), nullable=True)
 37 |     is_superuser: Mapped[bool] = mapped_column(Boolean, default=False)
 38 |     user_image: Mapped[str] = mapped_column(String(1048), nullable=True)
 39 |     settings: Mapped[dict[str, Any]] = mapped_column(JSONB, server_default="{}")
 40 |     is_deleted: Mapped[bool] = mapped_column(Boolean, default=False)  # redefine is_deleted column for index
 41 | 
 42 |     chats: Mapped[List["Chat"]] = relationship(secondary=chat_participant, back_populates="users")
 43 |     messages: Mapped[List["Message"]] = relationship(back_populates="user")
 44 |     read_statuses: Mapped[List["ReadStatus"]] = relationship(back_populates="user")
 45 | 
 46 |     def __str__(self):
 47 |         return f"{self.username}"
 48 | 
 49 |     # Indexes
 50 | 
 51 |     __table_args__ = (
 52 |         Index("idx_user_on_email_username", "email", "username", postgresql_concurrently=True),
 53 |         Index(
 54 |             "idx_user_partial_on_email_not_deleted",
 55 |             "email",
 56 |             "is_deleted",
 57 |             postgresql_concurrently=True,
 58 |             postgresql_where=(is_deleted.is_(False)),
 59 |         ),
 60 |     )
 61 | 
 62 | 
 63 | class Chat(BaseModel):
 64 |     __tablename__ = "chat"
 65 | 
 66 |     id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
 67 |     guid: Mapped[uuid.UUID] = mapped_column(UUID(as_uuid=True), default=uuid.uuid4)
 68 |     chat_type: Mapped[str] = mapped_column(Enum(ChatType, inherit_schema=True))
 69 | 
 70 |     users: Mapped[List["User"]] = relationship(secondary=chat_participant, back_populates="chats")
 71 |     messages: Mapped[List["Message"]] = relationship(back_populates="chat", cascade="all,delete")
 72 |     read_statuses: Mapped[List["ReadStatus"]] = relationship(back_populates="chat", cascade="all,delete")
 73 | 
 74 |     def __str__(self):
 75 |         return f"{self.chat_type.value.title()} {self.guid}"
 76 | 
 77 |     __table_args__ = (
 78 |         Index("idx_chat_on_is_deleted_chat_type", "is_deleted", "chat_type", postgresql_concurrently=True),
 79 |         Index("idx_chat_on_guid", "guid", postgresql_concurrently=True),
 80 |     )
 81 | 
 82 | 
 83 | class MessageType(enum.Enum):
 84 |     TEXT = "text"
 85 |     FILE = "file"
 86 | 
 87 | 
 88 | class Message(BaseModel):
 89 |     __tablename__ = "message"
 90 | 
 91 |     id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
 92 |     guid: Mapped[uuid.UUID] = mapped_column(UUID(as_uuid=True), default=uuid.uuid4)
 93 |     message_type: Mapped[str] = mapped_column(Enum(MessageType, inherit_schema=True), default=MessageType.TEXT)
 94 |     content: Mapped[str] = mapped_column(String(5000))
 95 |     user_id: Mapped[int] = mapped_column(ForeignKey("user.id"))
 96 |     chat_id: Mapped[int] = mapped_column(ForeignKey("chat.id"))
 97 | 
 98 |     file_name: Mapped[str] = mapped_column(String(50), nullable=True)
 99 |     file_path: Mapped[str] = mapped_column(String(1000), nullable=True)
100 | 
101 |     chat: Mapped["Chat"] = relationship(back_populates="messages")
102 |     user: Mapped["User"] = relationship(back_populates="messages")
103 | 
104 |     def __str__(self):
105 |         return f"{self.content}"
106 | 
107 |     __table_args__ = (
108 |         Index("idx_message_on_chat_id", "chat_id", postgresql_concurrently=True),
109 |         Index("idx_message_on_user_id", "user_id", postgresql_concurrently=True),
110 |         Index("idx_message_on_user_id_chat_id", "chat_id", "user_id", postgresql_concurrently=True),
111 |     )
112 | 
113 | 
114 | class ReadStatus(RemoveBaseFieldsMixin, BaseModel):
115 |     __tablename__ = "read_status"
116 | 
117 |     id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
118 |     last_read_message_id: Mapped[int] = mapped_column(nullable=True)
119 |     user_id: Mapped[int] = mapped_column(ForeignKey("user.id"))
120 |     chat_id: Mapped[int] = mapped_column(ForeignKey("chat.id"))
121 | 
122 |     # to display unread messages for a user in different chats
123 |     chat: Mapped["Chat"] = relationship(back_populates="read_statuses")
124 |     user: Mapped["User"] = relationship(back_populates="read_statuses")
125 | 
126 |     def __str__(self):
127 |         return f"User: {self.user_id}, Message: {self.last_read_message_id}"
128 | 
129 |     __table_args__ = (
130 |         Index("idx_read_status_on_chat_id", "chat_id", postgresql_concurrently=True),
131 |         Index("idx_read_status_on_user_id", "user_id", postgresql_concurrently=True),
132 |     )
133 | 


--------------------------------------------------------------------------------
/src/registration/router.py:
--------------------------------------------------------------------------------
 1 | import redis.asyncio as aioredis
 2 | from fastapi import APIRouter, BackgroundTasks, Depends, HTTPException, status
 3 | from sqlalchemy.ext.asyncio import AsyncSession
 4 | 
 5 | from src.database import get_async_session
 6 | from src.dependencies import get_cache
 7 | from src.models import User
 8 | from src.registration.schemas import UserRegisterSchema
 9 | from src.registration.services import ImageSaver, create_user, get_user_by_email_or_username
10 | from src.utils import clear_cache_for_all_users
11 | 
12 | account_router = APIRouter(tags=["Account Management"])
13 | 
14 | # user data and image in one endpoint
15 | # https://github.com/tiangolo/fastapi/issues/2257
16 | # https://stackoverflow.com/questions/60127234/how-to-use-a-pydantic-model-with-form-data-in-fastapi
17 | 
18 | 
19 | @account_router.post("/register/", summary="Register user")
20 | async def register_user(
21 |     background_tasks: BackgroundTasks,
22 |     db_session: AsyncSession = Depends(get_async_session),
23 |     cache: aioredis.Redis = Depends(get_cache),
24 |     user_schema: UserRegisterSchema = Depends(UserRegisterSchema),
25 | ):
26 |     # check if user with username or email already exists
27 |     if await get_user_by_email_or_username(db_session, email=user_schema.email, username=user_schema.username):
28 |         raise HTTPException(
29 |             status_code=status.HTTP_409_CONFLICT, detail="User with provided credentials already exists"
30 |         )
31 | 
32 |     try:
33 |         user: User = await create_user(db_session, user_schema=user_schema)
34 |         # background_task is executed after the response is sent to the client.
35 |         # this allows to save an image without affecting the user experience.
36 |         if uploaded_image := user_schema.uploaded_image:
37 |             image_saver = ImageSaver(db_session, user=user)
38 |             background_tasks.add_task(image_saver.save_user_image, user, uploaded_image)
39 | 
40 |     except Exception as excinfo:
41 |         raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail=f"{excinfo}")
42 |     else:
43 |         # clear cache for all users
44 |         await clear_cache_for_all_users(cache)
45 |         return "User has been successfully created"
46 | 


--------------------------------------------------------------------------------
/src/registration/schemas.py:
--------------------------------------------------------------------------------
 1 | from fastapi import File, Form, UploadFile
 2 | from pydantic import EmailStr
 3 | 
 4 | 
 5 | class UserRegisterSchema:
 6 |     def __init__(
 7 |         self,
 8 |         email: EmailStr = Form(...),
 9 |         username: str = Form(max_length=150),
10 |         password: str = Form(min_length=6, max_length=128),
11 |         first_name: str = Form(max_length=150),
12 |         last_name: str = Form(max_length=150),
13 |         uploaded_image: UploadFile = File(None, media_type="image/jpeg"),
14 |     ):
15 |         self.email = email
16 |         self.username = username
17 |         self.password = password
18 |         self.first_name = first_name
19 |         self.last_name = last_name
20 |         self.uploaded_image = uploaded_image
21 | 


--------------------------------------------------------------------------------
/src/registration/services.py:
--------------------------------------------------------------------------------
  1 | import logging
  2 | import os
  3 | from io import BytesIO
  4 | 
  5 | import aiofiles
  6 | from fastapi import UploadFile
  7 | from PIL import Image
  8 | from sqlalchemy import or_, select
  9 | from sqlalchemy.ext.asyncio import AsyncSession
 10 | 
 11 | from src.config import settings
 12 | from src.models import User
 13 | from src.registration.schemas import UserRegisterSchema
 14 | from src.utils import get_hashed_password
 15 | 
 16 | logger = logging.getLogger(__name__)
 17 | 
 18 | 
 19 | DEFAULT_CHUNK_SIZE = 1024 * 1024 * 1  # 1 megabyte
 20 | 
 21 | 
 22 | async def get_user_by_email_or_username(db_session: AsyncSession, *, email: str, username: str) -> User | None:
 23 |     query = select(User).where(or_(User.email == email, User.username == username))
 24 |     result = await db_session.execute(query)
 25 |     user: User | None = result.scalar_one_or_none()
 26 |     return user
 27 | 
 28 | 
 29 | async def create_user(db_session: AsyncSession, *, user_schema: UserRegisterSchema) -> User:
 30 |     hashed_password = get_hashed_password(user_schema.password)
 31 |     new_user = User(
 32 |         username=user_schema.username.lower(),
 33 |         email=user_schema.email.lower(),
 34 |         first_name=user_schema.first_name,
 35 |         last_name=user_schema.last_name,
 36 |         password=hashed_password,
 37 |     )
 38 | 
 39 |     db_session.add(new_user)
 40 |     await db_session.commit()
 41 | 
 42 |     return new_user
 43 | 
 44 | 
 45 | class ImageSaver:
 46 |     def __init__(self, db_session: AsyncSession, *, user: User):
 47 |         self.db_session: AsyncSession = db_session
 48 |         self.user: User = user
 49 | 
 50 |     async def save_user_image(self, user: User, uploaded_image: UploadFile) -> str | None:
 51 |         file_extension: str = uploaded_image.filename.split(".")[-1].lower()
 52 |         filename: str = f"{user.username}.{file_extension}"
 53 | 
 54 |         match settings.ENVIRONMENT:
 55 |             case "development":
 56 |                 return await self._save_image_to_static(user, uploaded_image, filename)
 57 |             case "production":
 58 |                 return await self._save_image_to_aws_bucket(user, uploaded_image, filename)
 59 |             case _:
 60 |                 logger.error(f"Unsupported environment: {settings.ENVIRONMENT}")
 61 |                 return None
 62 | 
 63 |     async def _save_image_to_static(self, user: User, uploaded_image: UploadFile, filename: str) -> str:
 64 |         """
 65 |         Handles the logic for saving user images to a static folder in a development environment.
 66 |         Saves user image to the static folder 'static/images/profile'.
 67 |         Returns the URL address where the image is saved.
 68 | 
 69 |         Parameters:
 70 |             - user (User): User object associated with the image.
 71 |             - uploaded_image (UploadFile): The image file uploaded.
 72 |             - filename (str): The desired filename for the saved image.
 73 | 
 74 |         Returns:
 75 |             - str: URL address where the image is saved.
 76 |         """
 77 |         # Ensure that the directory exists
 78 |         folder_path = "src/static/images/profile"
 79 |         os.makedirs(folder_path, exist_ok=True)
 80 | 
 81 |         # Create a temporary file path for saving the uploaded image
 82 |         file_path = f"{folder_path}/{filename}_temporary"
 83 | 
 84 |         # Load the image in chunks and save it to disk
 85 |         async with aiofiles.open(file_path, "wb") as f:
 86 |             while chunk := await uploaded_image.read(DEFAULT_CHUNK_SIZE):
 87 |                 await f.write(chunk)
 88 | 
 89 |         # Resize the saved image to 600x600
 90 |         resized_image: Image = self._resize_image(file_path)
 91 | 
 92 |         # Save the resized image with a modified file path
 93 |         resized_image_file_path = file_path.replace("_temporary", "")
 94 |         resized_image.save(resized_image_file_path, resized_image.format)
 95 | 
 96 |         # Remove the original uploaded image
 97 |         os.remove(file_path)
 98 | 
 99 |         # Form the URL address for the saved image
100 |         image_url = resized_image_file_path.replace("src/", "/")
101 | 
102 |         # Update user information with the image URL and commit to the database
103 |         user.user_image = image_url
104 |         await self.db_session.commit()
105 | 
106 |         return image_url
107 | 
108 |     async def _save_image_to_aws_bucket(self, user: User, uploaded_image: UploadFile, filename: str) -> str | None:
109 |         """
110 |         Handles the logic for uploading user images to an AWS S3 bucket in a production environment.
111 |         Returns URL address where image is saved.
112 | 
113 |         Parameters:
114 |             - user (User): User object associated with the image.
115 |             - uploaded_image (UploadFile): The image file uploaded
116 |             - filename (str): The desired filename for the saved image.
117 | 
118 |         Returns:
119 |             - str | None: URL address where the image is saved, or None if upload fails.
120 |         """
121 |         # Check if AWS client and bucket information are available
122 |         if (
123 |             (aws_client := settings.get_aws_client_for_image_upload(), aws_bucket := settings.AWS_IMAGES_BUCKET)
124 |             and aws_client
125 |             and aws_bucket
126 |         ):
127 |             # Read the content of the uploaded image
128 |             image_file: bytes = await uploaded_image.read()
129 | 
130 |             # Resize the image using the _resize_image method
131 |             resized_image: Image = self._resize_image(BytesIO(image_file))
132 | 
133 |             # Save the resized image to an in-memory file
134 |             with BytesIO() as in_memory_image_file:
135 |                 resized_image.save(in_memory_image_file, format=resized_image.format)
136 |                 in_memory_image_file.seek(0)
137 | 
138 |                 # Upload the resized image to AWS S3 bucket
139 |                 aws_client.upload_fileobj(
140 |                     in_memory_image_file, aws_bucket, filename, ExtraArgs={"ContentType": "image/jpeg"}
141 |                 )
142 | 
143 |             # Form the URL address for the saved image
144 |             image_url: str = f"https://{aws_bucket}.s3.amazonaws.com/{filename}"
145 | 
146 |             # Update user information with the image URL and commit to the database
147 |             user.user_image = image_url
148 |             await self.db_session.commit()
149 | 
150 |             return image_url
151 | 
152 |         else:
153 |             logger.error(f"AWS client or bucket information is missing when registering: {user.username}")
154 |             return None
155 | 
156 |     def _resize_image(self, file_path, width=600, height=600) -> Image:
157 |         image = Image.open(file_path)
158 |         image_format: str | None = image.format
159 |         resized_image: Image = image.resize((width, height), Image.LANCZOS)
160 |         resized_image.format = image_format
161 | 
162 |         return resized_image
163 | 


--------------------------------------------------------------------------------
/src/routers.py:
--------------------------------------------------------------------------------
 1 | from src.authentication.router import auth_router
 2 | from src.chat.router import chat_router
 3 | from src.contact.router import contact_router
 4 | from src.registration.router import account_router
 5 | from src.settings.router import settings_router
 6 | from src.websocket.router import websocket_router
 7 | 
 8 | routers = [
 9 |     websocket_router,
10 |     account_router,
11 |     auth_router,
12 |     chat_router,
13 |     contact_router,
14 |     settings_router,
15 | ]
16 | 


--------------------------------------------------------------------------------
/src/settings/router.py:
--------------------------------------------------------------------------------
 1 | import logging
 2 | 
 3 | from fastapi import APIRouter, Depends
 4 | from fastapi_limiter.depends import RateLimiter
 5 | from sqlalchemy.ext.asyncio import AsyncSession
 6 | 
 7 | from src.database import get_async_session
 8 | from src.dependencies import get_current_user
 9 | from src.models import User
10 | from src.settings.schemas import UserThemeSchema
11 | from src.settings.services import set_user_theme
12 | 
13 | settings_router = APIRouter(tags=["Settings Management"])
14 | 
15 | logger = logging.getLogger(__name__)
16 | 
17 | 
18 | @settings_router.post(
19 |     "/user/settings/theme/", dependencies=[Depends(RateLimiter(times=10, minutes=1))], summary="Set user theme"
20 | )
21 | async def set_user_theme_view(
22 |     user_theme_schema: UserThemeSchema,
23 |     db_session: AsyncSession = Depends(get_async_session),
24 |     current_user: User = Depends(get_current_user),
25 | ):
26 |     await set_user_theme(db_session, user=current_user, theme=user_theme_schema.theme)
27 | 
28 |     return {"message": "New theme has been set"}
29 | 


--------------------------------------------------------------------------------
/src/settings/schemas.py:
--------------------------------------------------------------------------------
 1 | from enum import Enum
 2 | 
 3 | from pydantic import BaseModel
 4 | 
 5 | 
 6 | class ThemeEnum(str, Enum):
 7 |     teal = "teal"
 8 |     midnight = "midnight"
 9 | 
10 | 
11 | class UserThemeSchema(BaseModel):
12 |     theme: ThemeEnum
13 | 


--------------------------------------------------------------------------------
/src/settings/services.py:
--------------------------------------------------------------------------------
 1 | from sqlalchemy.ext.asyncio import AsyncSession
 2 | from sqlalchemy.orm.attributes import flag_modified
 3 | 
 4 | from src.models import User
 5 | from src.settings.schemas import ThemeEnum
 6 | 
 7 | 
 8 | async def set_user_theme(db_session: AsyncSession, *, user: User, theme: ThemeEnum) -> None:
 9 |     user.settings["theme"] = theme.value
10 |     flag_modified(user, "settings")
11 | 
12 |     await db_session.commit()
13 | 


--------------------------------------------------------------------------------
/src/static/.gitkeep:
--------------------------------------------------------------------------------
https://raw.githubusercontent.com/notarious2/fastapi-chat/e8a7d847107e316df31f84e5e2440317fecd3dfd/src/static/.gitkeep


--------------------------------------------------------------------------------
/src/utils.py:
--------------------------------------------------------------------------------
 1 | from uuid import UUID
 2 | 
 3 | import redis.asyncio as aioredis
 4 | from passlib.context import CryptContext
 5 | 
 6 | from src.models import User
 7 | 
 8 | password_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
 9 | 
10 | 
11 | def get_hashed_password(password: str) -> str:
12 |     return password_context.hash(password)
13 | 
14 | 
15 | def verify_password(plain_password: str, hashed_password: str) -> bool:
16 |     return password_context.verify(plain_password, hashed_password)
17 | 
18 | 
19 | async def clear_cache_for_get_messages(cache: aioredis.Redis, chat_guid: UUID):
20 |     pattern_for_get_messages = f"messages_{chat_guid}_*"
21 |     keys_found = cache.scan_iter(match=pattern_for_get_messages)
22 |     async for key in keys_found:
23 |         await cache.delete(key)
24 | 
25 | 
26 | async def clear_cache_for_get_direct_chats(cache: aioredis.Redis, user: User):
27 |     pattern_for_get_direct_chats = f"direct_chats_{user.guid}"
28 |     keys_found = cache.scan_iter(match=pattern_for_get_direct_chats)
29 |     async for key in keys_found:
30 |         await cache.delete(key)
31 | 
32 | 
33 | async def clear_cache_for_all_users(cache: aioredis.Redis):
34 |     keys_found = cache.scan_iter(match="*all_users")
35 |     async for key in keys_found:
36 |         await cache.delete(key)
37 | 


--------------------------------------------------------------------------------
/src/websocket/exceptions.py:
--------------------------------------------------------------------------------
1 | class WebsocketTooManyRequests(Exception):
2 |     pass
3 | 


--------------------------------------------------------------------------------
/src/websocket/handlers.py:
--------------------------------------------------------------------------------
  1 | import asyncio
  2 | import logging
  3 | from datetime import datetime
  4 | 
  5 | import redis.asyncio as aioredis
  6 | from fastapi import WebSocket
  7 | from fastapi.encoders import jsonable_encoder
  8 | from sqlalchemy.ext.asyncio import AsyncSession
  9 | 
 10 | from src.managers.websocket_manager import WebSocketManager
 11 | from src.models import Chat, Message, ReadStatus, User
 12 | from src.utils import clear_cache_for_get_direct_chats, clear_cache_for_get_messages
 13 | from src.websocket.schemas import (
 14 |     AddUserToChatSchema,
 15 |     MessageReadSchema,
 16 |     NotifyChatRemovedSchema,
 17 |     ReceiveMessageSchema,
 18 |     SendMessageSchema,
 19 |     UserTypingSchema,
 20 | )
 21 | from src.websocket.services import (
 22 |     get_chat_id_by_guid,
 23 |     get_message_by_guid,
 24 |     mark_last_read_message,
 25 |     mark_user_as_online,
 26 |     send_new_chat_created_ws_message,
 27 | )
 28 | 
 29 | logger = logging.getLogger(__name__)
 30 | 
 31 | 
 32 | socket_manager = WebSocketManager()
 33 | 
 34 | 
 35 | @socket_manager.handler("new_message")
 36 | async def new_message_handler(
 37 |     websocket: WebSocket,
 38 |     db_session: AsyncSession,
 39 |     cache: aioredis.Redis,
 40 |     incoming_message: dict,
 41 |     chats: dict,
 42 |     current_user: User,
 43 |     cache_enabled: bool,
 44 |     **kwargs,
 45 | ):
 46 |     """
 47 |     message is received as "new_message" type but broadcasted to all users
 48 |     as "new" type
 49 |     """
 50 |     message_schema = ReceiveMessageSchema(**incoming_message)
 51 |     chat_guid: str = str(message_schema.chat_guid)
 52 | 
 53 |     notify_friend_about_new_chat: bool = False
 54 |     # newly created chat
 55 |     if not chats or chat_guid not in chats:
 56 |         chat_id: int | None = await get_chat_id_by_guid(db_session, chat_guid=chat_guid)
 57 |         if chat_id:
 58 |             # this action modifies chats variable in websocket view
 59 |             chats[chat_guid] = chat_id
 60 |             await socket_manager.add_user_to_chat(chat_guid, websocket)
 61 |             # must notify friend that new chat has been created
 62 |             notify_friend_about_new_chat = True
 63 | 
 64 |         else:
 65 |             await socket_manager.send_error("Chat has not been added", websocket)
 66 |             return
 67 | 
 68 |     chat_id = chats.get(chat_guid)
 69 |     try:
 70 |         # Save message and broadcast it back
 71 |         message = Message(
 72 |             content=message_schema.content,
 73 |             chat_id=chat_id,
 74 |             user_id=current_user.id,
 75 |         )
 76 |         db_session.add(message)
 77 |         await db_session.flush()  # to generate id
 78 | 
 79 |         # Update the updated_at field of the chat
 80 |         chat: Chat = await db_session.get(Chat, chat_id)
 81 |         chat.updated_at = datetime.now()
 82 |         db_session.add(chat)
 83 | 
 84 |         await db_session.commit()
 85 |         await db_session.refresh(message, attribute_names=["user", "chat"])  # ?
 86 |         await db_session.refresh(chat, attribute_names=["users"])  # ?
 87 | 
 88 |     except Exception as exc_info:
 89 |         await db_session.rollback()
 90 |         logger.exception(f"[new_message] Exception, rolling back session, detail: {exc_info}")
 91 |         raise exc_info
 92 | 
 93 |     await mark_user_as_online(
 94 |         cache=cache, current_user=current_user, socket_manager=socket_manager, chat_guid=chat_guid
 95 |     )
 96 |     # clear cache for all users
 97 |     if cache_enabled:
 98 |         for user in chat.users:
 99 |             await clear_cache_for_get_direct_chats(cache=cache, user=user)
100 |         # clear cache for getting messages
101 |         await clear_cache_for_get_messages(cache=cache, chat_guid=chat_guid)
102 | 
103 |     send_message_schema = SendMessageSchema(
104 |         message_guid=message.guid,
105 |         chat_guid=chat.guid,
106 |         user_guid=current_user.guid,
107 |         content=message.content,
108 |         created_at=message.created_at,
109 |         is_read=False,
110 |         is_new=True,
111 |     )
112 |     outgoing_message: dict = send_message_schema.model_dump_json()
113 | 
114 |     await socket_manager.broadcast_to_chat(chat_guid, outgoing_message)
115 | 
116 |     if notify_friend_about_new_chat:
117 |         logger.info("Notifying friend about newly created chat")
118 |         await send_new_chat_created_ws_message(socket_manager=socket_manager, current_user=current_user, chat=chat)
119 | 
120 | 
121 | @socket_manager.handler("message_read")
122 | async def message_read_handler(
123 |     websocket: WebSocket,
124 |     db_session: AsyncSession,
125 |     incoming_message: dict,
126 |     chats: dict,
127 |     current_user: User,
128 |     cache: aioredis.Redis,
129 |     cache_enabled: bool,
130 |     **kwargs,
131 | ):
132 |     message_read_schema = MessageReadSchema(**incoming_message)
133 | 
134 |     message_guid = str(message_read_schema.message_guid)
135 |     message: Message | None = await get_message_by_guid(db_session, message_guid=message_guid)
136 |     if not message:
137 |         await socket_manager.send_error(
138 |             f"[read_status] Message with provided guid [{message_guid}] does not exist", websocket
139 |         )
140 |     chat_guid = str(message_read_schema.chat_guid)
141 |     if chat_guid not in chats:
142 |         await socket_manager.send_error(
143 |             f"[read_status] Chat with provided guid [{chat_guid}] does not exist", websocket
144 |         )
145 |         return
146 |     chat_id = chats.get(chat_guid)
147 | 
148 |     # Mark message read for own user, if none is returned, message is already read
149 |     read_status: ReadStatus | None = await mark_last_read_message(
150 |         db_session, user_id=current_user.id, chat_id=chat_id, last_read_message_id=message.id
151 |     )
152 |     if read_status:
153 |         outgoing_message = {
154 |             "type": "message_read",
155 |             "user_guid": str(current_user.guid),
156 |             "chat_guid": str(chat_guid),
157 |             "last_read_message_guid": str(message.guid),
158 |             "last_read_message_created_at": str(message.created_at),
159 |         }
160 |         # change redis/send ws message showing status is online
161 |         await mark_user_as_online(
162 |             cache=cache, current_user=current_user, socket_manager=socket_manager, chat_guid=chat_guid
163 |         )
164 |         if cache_enabled:
165 |             # clear cache for getting messages
166 |             await clear_cache_for_get_messages(cache=cache, chat_guid=chat_guid)
167 | 
168 |         await socket_manager.broadcast_to_chat(chat_guid, outgoing_message)
169 | 
170 | 
171 | @socket_manager.handler("user_typing")
172 | async def user_typing_handler(
173 |     websocket: WebSocket,
174 |     incoming_message: dict,
175 |     chats: dict,
176 |     current_user: User,
177 |     cache: aioredis.Redis,
178 |     **kwargs,
179 | ):
180 |     # TODO: Rate limit
181 |     # TODO: Validate chat_guid and user_guid
182 | 
183 |     user_typing_schema = UserTypingSchema(**incoming_message)
184 |     chat_guid: str = str(user_typing_schema.chat_guid)
185 |     if chat_guid not in chats:
186 |         await socket_manager.send_error(
187 |             f"[user_typing] Chat with provided guid [{chat_guid}] does not exist", websocket
188 |         )
189 |         return
190 | 
191 |     await mark_user_as_online(
192 |         cache=cache, current_user=current_user, socket_manager=socket_manager, chat_guid=chat_guid
193 |     )
194 | 
195 |     outgoing_message: dict = user_typing_schema.model_dump_json()
196 |     await socket_manager.broadcast_to_chat(chat_guid, outgoing_message)
197 | 
198 | 
199 | @socket_manager.handler("add_user_to_chat")
200 | async def add_user_to_chat_handler(
201 |     websocket: WebSocket,
202 |     incoming_message: dict,
203 |     chats: dict,
204 |     current_user: User,
205 |     cache: aioredis.Redis,
206 |     **kwargs,
207 | ):
208 |     """
209 |     `add_user_to_chat` type is only received by non-initiator user active websockets
210 |     it subscribes the user to the newly created chat and marks non-initiator user
211 |     as active since he/she has an active websocket connection that received this message
212 |     """
213 |     add_user_to_chat_schema = AddUserToChatSchema(**incoming_message)
214 | 
215 |     chat_guid = add_user_to_chat_schema.chat_guid
216 |     chat_id = add_user_to_chat_schema.chat_id
217 | 
218 |     await socket_manager.add_user_to_chat(chat_guid=chat_guid, websocket=websocket)
219 |     # modify chats variable in websocket view
220 |     chats[chat_guid] = chat_id
221 | 
222 |     await mark_user_as_online(
223 |         cache=cache, current_user=current_user, socket_manager=socket_manager, chat_guid=chat_guid
224 |     )
225 | 
226 | 
227 | @socket_manager.handler("chat_deleted")
228 | async def chat_deleted_handler(
229 |     websocket: WebSocket,
230 |     incoming_message: dict,
231 |     chats: dict,
232 |     current_user: User,
233 |     **kwargs,
234 | ):
235 |     """
236 |     `chat_deleted` - sends ws notification to all active websocket connections to display
237 |     a message that the chat has been deleted/actual deletion happens via HTTP request
238 |     """
239 | 
240 |     notify_chat_removed_schema = NotifyChatRemovedSchema(**incoming_message)
241 |     chat_guid = notify_chat_removed_schema.chat_guid
242 |     if chat_guid not in chats:
243 |         await socket_manager.send_error(
244 |             f"[chat_deleted] Chat with provided guid [{chat_guid}] does not exist", websocket
245 |         )
246 |         return
247 | 
248 |     # get all websocket connections that belong to this chat (except for ws that sent this messsage)
249 |     # and send notification that chat has been removed
250 | 
251 |     target_websockets: set[WebSocket] = socket_manager.chats.get(chat_guid)
252 | 
253 |     outgoing_message = {
254 |         "type": "chat_deleted",
255 |         "user_guid": str(current_user.guid),
256 |         "user_name": current_user.first_name,
257 |         "chat_guid": chat_guid,
258 |     }
259 | 
260 |     if target_websockets:
261 |         # Send the notification message to the target user concurrently
262 |         # used to notify frontend
263 |         await asyncio.gather(
264 |             *[
265 |                 socket.send_json(jsonable_encoder(outgoing_message))
266 |                 for socket in target_websockets
267 |                 if socket != websocket
268 |             ]
269 |         )
270 | 


--------------------------------------------------------------------------------
/src/websocket/rate_limiter.py:
--------------------------------------------------------------------------------
1 | from src.websocket.exceptions import WebsocketTooManyRequests
2 | 
3 | 
4 | async def websocket_callback(ws, pexpire):
5 |     raise WebsocketTooManyRequests("Too many requests")
6 | 


--------------------------------------------------------------------------------
/src/websocket/router.py:
--------------------------------------------------------------------------------
  1 | import asyncio
  2 | import logging
  3 | from json.decoder import JSONDecodeError
  4 | 
  5 | import redis.asyncio as aioredis
  6 | from fastapi import APIRouter, Depends, WebSocket, WebSocketDisconnect
  7 | from fastapi_limiter.depends import WebSocketRateLimiter
  8 | from sqlalchemy.ext.asyncio import AsyncSession
  9 | 
 10 | from src.database import get_async_session
 11 | from src.dependencies import get_cache, get_cache_setting, get_current_user
 12 | from src.models import User
 13 | from src.websocket.exceptions import WebsocketTooManyRequests
 14 | from src.websocket.handlers import socket_manager
 15 | from src.websocket.rate_limiter import websocket_callback
 16 | from src.websocket.services import (
 17 |     check_user_statuses,
 18 |     get_user_active_direct_chats,
 19 |     mark_user_as_offline,
 20 |     mark_user_as_online,
 21 | )
 22 | 
 23 | logger = logging.getLogger(__name__)
 24 | 
 25 | 
 26 | websocket_router = APIRouter()
 27 | 
 28 | 
 29 | @websocket_router.websocket("/ws/")
 30 | async def websocket_endpoint(
 31 |     websocket: WebSocket,
 32 |     current_user: User = Depends(get_current_user),
 33 |     db_session: AsyncSession = Depends(get_async_session),
 34 |     cache: aioredis.Redis = Depends(get_cache),
 35 |     cache_enabled: bool = Depends(get_cache_setting),
 36 | ):
 37 |     await socket_manager.connect_socket(websocket=websocket)
 38 |     logger.info("Websocket connection is established")
 39 |     ratelimit = WebSocketRateLimiter(times=50, seconds=10, callback=websocket_callback)
 40 | 
 41 |     # add user's socket connection {user_guid: {ws1, ws2}}
 42 |     await socket_manager.add_user_socket_connection(str(current_user.guid), websocket)
 43 | 
 44 |     # Update the user's status in Redis with a new TTL
 45 |     await mark_user_as_online(cache=cache, current_user=current_user)
 46 |     # get all users direct chats and subscribe to each
 47 |     # guid/id key-value pair to easily get chat_id based on chat_guid later
 48 |     if chats := await get_user_active_direct_chats(db_session, current_user=current_user):
 49 |         # subscribe this websocket instance to all Redis PubSub channels
 50 |         for chat_guid in chats:
 51 |             await socket_manager.add_user_to_chat(chat_guid, websocket)
 52 |     else:
 53 |         chats = dict()
 54 | 
 55 |     # task for sending status messages, not dependent on cache_enabled
 56 |     user_status_task = asyncio.create_task(check_user_statuses(cache, socket_manager, current_user, chats))
 57 | 
 58 |     try:
 59 |         while True:
 60 |             try:
 61 |                 incoming_message = await websocket.receive_json()
 62 |                 await ratelimit(websocket)
 63 | 
 64 |                 message_type = incoming_message.get("type")
 65 |                 if not message_type:
 66 |                     await socket_manager.send_error("You should provide message type", websocket)
 67 |                     continue
 68 | 
 69 |                 handler = socket_manager.handlers.get(message_type)
 70 | 
 71 |                 if not handler:
 72 |                     logger.error(f"No handler [{message_type}] exists")
 73 |                     await socket_manager.send_error(f"Type: {message_type} was not found", websocket)
 74 |                     continue
 75 | 
 76 |                 await handler(
 77 |                     websocket=websocket,
 78 |                     db_session=db_session,
 79 |                     cache=cache,
 80 |                     incoming_message=incoming_message,
 81 |                     chats=chats,
 82 |                     current_user=current_user,
 83 |                     cache_enabled=cache_enabled,
 84 |                 )
 85 | 
 86 |             except (JSONDecodeError, AttributeError) as excinfo:
 87 |                 logger.exception(f"Websocket error, detail: {excinfo}")
 88 |                 await socket_manager.send_error("Wrong message format", websocket)
 89 |                 continue
 90 |             except ValueError as excinfo:
 91 |                 logger.exception(f"Websocket error, detail: {excinfo}")
 92 |                 await socket_manager.send_error("Could not validate incoming message", websocket)
 93 | 
 94 |             except WebsocketTooManyRequests:
 95 |                 logger.exception(f"User: {current_user} sent too many ws requests")
 96 |                 await socket_manager.send_error("You have sent too many requests", websocket)
 97 | 
 98 |     except WebSocketDisconnect:
 99 |         logging.info("Websocket is disconnected")
100 |         # unsubscribe user websocket connection from all chats
101 |         if chats:
102 |             for chat_guid in chats:
103 |                 await socket_manager.remove_user_from_chat(chat_guid, websocket)
104 |                 await mark_user_as_offline(
105 |                     cache=cache, socket_manager=socket_manager, current_user=current_user, chat_guid=chat_guid
106 |                 )
107 |             await socket_manager.pubsub_client.disconnect()
108 | 
109 |         user_status_task.cancel()
110 |         await socket_manager.remove_user_guid_to_websocket(user_guid=str(current_user.guid), websocket=websocket)
111 | 


--------------------------------------------------------------------------------
/src/websocket/schemas.py:
--------------------------------------------------------------------------------
 1 | from datetime import datetime
 2 | 
 3 | from pydantic import UUID4, BaseModel
 4 | 
 5 | # TODO: Add data validation
 6 | 
 7 | 
 8 | class ReceiveMessageSchema(BaseModel):
 9 |     user_guid: UUID4
10 |     chat_guid: UUID4
11 |     content: str
12 | 
13 | 
14 | class SendMessageSchema(BaseModel):
15 |     type: str = "new"
16 |     message_guid: UUID4
17 |     user_guid: UUID4
18 |     chat_guid: UUID4
19 |     content: str
20 |     created_at: datetime
21 |     is_read: bool | None = False
22 | 
23 | 
24 | class MessageReadSchema(BaseModel):
25 |     type: str
26 |     chat_guid: UUID4
27 |     message_guid: UUID4
28 | 
29 | 
30 | class UserTypingSchema(BaseModel):
31 |     type: str
32 |     chat_guid: UUID4
33 |     user_guid: UUID4
34 | 
35 | 
36 | class NewChatCreated(BaseModel):
37 |     type: str = "new_chat_created"
38 |     chat_id: int  # need to pass for guid/id mapping [chats variable]
39 |     chat_guid: UUID4
40 |     created_at: datetime
41 |     updated_at: datetime
42 |     friend: dict
43 |     has_new_messages: bool
44 |     new_messages_count: int
45 | 
46 | 
47 | class AddUserToChatSchema(BaseModel):
48 |     chat_guid: str  # used for websocket communication
49 |     chat_id: int
50 | 
51 | 
52 | class NotifyChatRemovedSchema(BaseModel):
53 |     type: str = "chat_deleted"
54 |     chat_guid: str
55 | 


--------------------------------------------------------------------------------
/src/websocket/services.py:
--------------------------------------------------------------------------------
  1 | import asyncio
  2 | import logging
  3 | from typing import Dict
  4 | from uuid import UUID
  5 | 
  6 | import redis.asyncio as aioredis
  7 | from fastapi import WebSocket
  8 | from fastapi.encoders import jsonable_encoder
  9 | from sqlalchemy import and_, select
 10 | from sqlalchemy.ext.asyncio import AsyncSession
 11 | from sqlalchemy.orm import selectinload
 12 | 
 13 | from src.config import settings
 14 | from src.managers.websocket_manager import WebSocketManager
 15 | from src.models import Chat, ChatType, Message, ReadStatus, User
 16 | from src.websocket.schemas import NewChatCreated
 17 | 
 18 | logger = logging.getLogger(__name__)
 19 | 
 20 | 
 21 | async def check_user_statuses(cache: aioredis.Redis, socket_manager: WebSocketManager, current_user: User, chats: dict):
 22 |     while True:
 23 |         is_online = await cache.exists(f"user:{current_user.id}:status")
 24 |         # make sure to translate to all chats that the user is in
 25 |         if not chats:
 26 |             return
 27 |         user_chat_guids = set(chats.keys()) & {str(chat.guid) for chat in current_user.chats}
 28 | 
 29 |         if is_online:
 30 |             for chat_guid in user_chat_guids:
 31 |                 # Update the user's status as "online" on the frontend
 32 |                 await socket_manager.broadcast_to_chat(
 33 |                     chat_guid,
 34 |                     {
 35 |                         "type": "status",
 36 |                         "username": current_user.username,
 37 |                         "user_guid": str(current_user.guid),
 38 |                         "status": "online",
 39 |                     },
 40 |                 )
 41 | 
 42 |         else:
 43 |             for chat_guid in user_chat_guids:
 44 |                 # Update the user's status as "inactive" on the frontend
 45 |                 await socket_manager.broadcast_to_chat(
 46 |                     chat_guid,
 47 |                     {
 48 |                         "type": "status",
 49 |                         "username": current_user.username,
 50 |                         "user_guid": str(current_user.guid),
 51 |                         "status": "inactive",
 52 |                     },
 53 |                 )
 54 |         await asyncio.sleep(settings.SECONDS_TO_SEND_USER_STATUS)  # Sleep for 60 seconds before the next check
 55 | 
 56 | 
 57 | async def mark_user_as_offline(
 58 |     cache: aioredis.Redis, socket_manager: WebSocketManager, current_user: User, chat_guid: str
 59 | ):
 60 |     await cache.delete(f"user:{current_user.id}:status")
 61 |     await socket_manager.broadcast_to_chat(
 62 |         chat_guid,
 63 |         {"type": "status", "username": current_user.username, "user_guid": str(current_user.guid), "status": "offline"},
 64 |     )
 65 | 
 66 | 
 67 | async def mark_user_as_online(
 68 |     cache: aioredis.Redis, current_user: User, socket_manager: WebSocketManager = None, chat_guid: str = None
 69 | ):
 70 |     # set new redis key
 71 |     await cache.set(f"user:{current_user.id}:status", "online", ex=60)  # 1 hours
 72 |     if socket_manager and chat_guid:
 73 |         await socket_manager.broadcast_to_chat(
 74 |             chat_guid,
 75 |             {
 76 |                 "type": "status",
 77 |                 "username": current_user.username,
 78 |                 "user_guid": str(current_user.guid),
 79 |                 "status": "online",
 80 |             },
 81 |         )
 82 | 
 83 | 
 84 | async def get_message_by_guid(db_session: AsyncSession, *, message_guid: UUID) -> Message | None:
 85 |     query = select(Message).where(and_(Message.guid == message_guid, Message.is_deleted.is_(False)))
 86 |     result = await db_session.execute(query)
 87 |     message: Message | None = result.scalar_one_or_none()
 88 | 
 89 |     return message
 90 | 
 91 | 
 92 | async def mark_last_read_message(
 93 |     db_session: AsyncSession, *, user_id: int, chat_id: int, last_read_message_id: int
 94 | ) -> ReadStatus | None:
 95 |     query = select(ReadStatus).where(and_(ReadStatus.user_id == user_id, ReadStatus.chat_id == chat_id))
 96 |     result = await db_session.execute(query)
 97 |     read_status: ReadStatus | None = result.scalar_one_or_none()
 98 | 
 99 |     if not read_status:
100 |         read_status = ReadStatus(user_id=user_id, chat_id=chat_id)
101 |         read_status.last_read_message_id = last_read_message_id
102 |     else:
103 |         if read_status.last_read_message_id >= last_read_message_id:
104 |             logging.warn(
105 |                 f"This message has been already read, details: "
106 |                 f"user_id: {user_id}, chat_id: {chat_id}, last_read_message_id: {last_read_message_id}"
107 |             )
108 |             return
109 |         else:
110 |             read_status.last_read_message_id = last_read_message_id
111 | 
112 |     db_session.add(read_status)
113 |     await db_session.commit()
114 | 
115 |     return read_status
116 | 
117 | 
118 | async def get_read_status(db_session: AsyncSession, *, user_id: int, chat_id: int) -> ReadStatus | None:
119 |     query = select(ReadStatus).where(and_(ReadStatus.user_id == user_id, ReadStatus.chat_id == chat_id))
120 |     result = await db_session.execute(query)
121 |     read_status: ReadStatus | None = result.scalar_one_or_none()
122 | 
123 |     return read_status
124 | 
125 | 
126 | async def get_user_active_direct_chats(db_session: AsyncSession, *, current_user: User) -> Dict[str, int] | None:
127 |     """
128 |     Returns a dictionary chat_guid: chat_id key-value pair for a given user
129 |     """
130 |     direct_chats_dict = dict()
131 |     query = (
132 |         select(Chat)
133 |         .where(and_(Chat.chat_type == ChatType.DIRECT, Chat.is_deleted.is_(False), Chat.users.contains(current_user)))
134 |         .options(selectinload(Chat.users))
135 |     )
136 |     result = await db_session.execute(query)
137 |     direct_chats: list[Chat] = result.scalars().all()
138 | 
139 |     if direct_chats:
140 |         for direct_chat in direct_chats:
141 |             direct_chats_dict[str(direct_chat.guid)] = direct_chat.id
142 |         return direct_chats_dict
143 | 
144 | 
145 | async def get_chat_id_by_guid(db_session: AsyncSession, *, chat_guid: UUID) -> int | None:
146 |     query = select(Chat.id).where(Chat.guid == chat_guid)
147 |     result = await db_session.execute(query)
148 |     chat_id: int | None = result.scalar_one_or_none()
149 | 
150 |     return chat_id
151 | 
152 | 
153 | async def send_new_chat_created_ws_message(socket_manager: WebSocketManager, current_user: User, chat: Chat):
154 |     """
155 |     Send a new chat created message to friend's websocket connections in the chat.
156 |     This allows to update chats while user is still connected without refreshing the page
157 |     via get_chats view
158 |     """
159 |     # current user becomes a friend for a user that receives this message
160 |     friend: dict = {
161 |         "guid": current_user.guid,
162 |         "first_name": current_user.first_name,
163 |         "last_name": current_user.last_name,
164 |         "username": current_user.username,
165 |         "user_image": current_user.user_image,
166 |     }
167 | 
168 |     # get friend guid
169 |     friend_guid: UUID | None = next((user.guid for user in chat.users if not user == current_user), None)
170 | 
171 |     if friend_guid is None:
172 |         logger.error("Friend guid not found", extra={"type": "new_chat_created", "friend_guid": friend_guid})
173 |         return
174 | 
175 |     payload = NewChatCreated(
176 |         chat_id=chat.id,
177 |         chat_guid=chat.guid,
178 |         created_at=chat.created_at,
179 |         updated_at=chat.updated_at,
180 |         friend=friend,
181 |         has_new_messages=True,
182 |         new_messages_count=1,
183 |     )
184 | 
185 |     target_websockets: set[WebSocket] = socket_manager.user_guid_to_websocket.get(str(friend_guid))
186 | 
187 |     # send new chat data to each target websocket
188 |     if target_websockets:
189 |         # Send the notification message to the target user concurrently
190 |         # used to notify frontend
191 |         await asyncio.gather(*[socket.send_json(jsonable_encoder(payload)) for socket in target_websockets])
192 |         return
193 | 
194 |     logger.debug(
195 |         "User has no active websocket connections", extra={"type": "new_chat_created", "friend_guid": friend_guid}
196 |     )
197 | 


--------------------------------------------------------------------------------
/templates/index.html:
--------------------------------------------------------------------------------
1 | {% extends "layout.html" %}
2 | {% block content %}
3 | <div class="col-12 col-xxl-6">
4 |     <div class="card mt-2 p-2 align-items-center">
5 |         <h2>Chat App Admin Panel</h2>
6 |     </div>
7 | </div>
8 | {% endblock %}


--------------------------------------------------------------------------------
/tests/__init__.py:
--------------------------------------------------------------------------------
https://raw.githubusercontent.com/notarious2/fastapi-chat/e8a7d847107e316df31f84e5e2440317fecd3dfd/tests/__init__.py


--------------------------------------------------------------------------------
/tests/authentication/test_authentication_router.py:
--------------------------------------------------------------------------------
 1 | from unittest import mock
 2 | 
 3 | from fastapi import status
 4 | from httpx import AsyncClient
 5 | 
 6 | from src.models import User
 7 | 
 8 | 
 9 | async def test_user_login_succeeds_given_valid_credentials(async_client: AsyncClient, bob_user: User):
10 |     url = "/login/"
11 |     payload = {"username": bob_user.username, "password": "password"}
12 | 
13 |     response = await async_client.post(url, data=payload)
14 | 
15 |     assert response.status_code == status.HTTP_200_OK
16 |     assert response.json() == {
17 |         "first_name": bob_user.first_name,
18 |         "last_name": bob_user.last_name,
19 |         "email": bob_user.email,
20 |         "username": bob_user.username,
21 |         "user_guid": str(bob_user.guid),
22 |         "user_image": None,
23 |         "settings": {},
24 |     }
25 | 
26 |     assert response.cookies["access_token"] == mock.ANY
27 |     assert response.cookies["refresh_token"] == mock.ANY
28 | 
29 | 
30 | async def test_user_login_succeeds_given_capitalized_username(async_client: AsyncClient, bob_user: User):
31 |     url = "/login/"
32 |     payload = {"username": bob_user.username.capitalize(), "password": "password"}
33 | 
34 |     response = await async_client.post(url, data=payload)
35 | 
36 |     assert response.status_code == status.HTTP_200_OK
37 |     assert response.json() == {
38 |         "first_name": bob_user.first_name,
39 |         "last_name": bob_user.last_name,
40 |         "email": bob_user.email,
41 |         "username": bob_user.username,
42 |         "user_guid": str(bob_user.guid),
43 |         "user_image": None,
44 |         "settings": {},
45 |     }
46 | 
47 |     assert response.cookies["access_token"] == mock.ANY
48 |     assert response.cookies["refresh_token"] == mock.ANY
49 | 
50 | 
51 | async def test_user_login_succeeds_given_capitalized_email(async_client: AsyncClient, bob_user: User):
52 |     url = "/login/"
53 |     payload = {"username": bob_user.email.capitalize(), "password": "password"}
54 | 
55 |     response = await async_client.post(url, data=payload)
56 | 
57 |     assert response.status_code == status.HTTP_200_OK
58 |     assert response.json() == {
59 |         "first_name": bob_user.first_name,
60 |         "last_name": bob_user.last_name,
61 |         "email": bob_user.email,
62 |         "username": bob_user.username,
63 |         "user_guid": str(bob_user.guid),
64 |         "user_image": None,
65 |         "settings": {},
66 |     }
67 | 
68 |     assert response.cookies["access_token"] == mock.ANY
69 |     assert response.cookies["refresh_token"] == mock.ANY
70 | 
71 | 
72 | async def test_user_login_fails_given_invalid_username(async_client: AsyncClient):
73 |     url = "/login/"
74 |     payload = {"username": "some username", "password": "password"}
75 | 
76 |     response = await async_client.post(url, data=payload)
77 | 
78 |     assert response.status_code == status.HTTP_400_BAD_REQUEST
79 |     assert response.json() == {"detail": "Incorrect email/username or password"}
80 | 
81 | 
82 | async def test_user_login_fails_given_invalid_password(async_client: AsyncClient, bob_user: User):
83 |     url = "/login/"
84 |     payload = {"username": bob_user.username, "password": "wrong_password"}
85 | 
86 |     response = await async_client.post(url, data=payload)
87 | 
88 |     assert response.status_code == status.HTTP_400_BAD_REQUEST
89 |     assert response.json() == {"detail": "Incorrect email/username or password"}
90 | 


--------------------------------------------------------------------------------
/tests/chat/router/test_create_chat.py:
--------------------------------------------------------------------------------
  1 | from unittest import mock
  2 | from unittest.mock import AsyncMock
  3 | from uuid import uuid4
  4 | 
  5 | import pytest
  6 | from fastapi import status
  7 | from httpx import AsyncClient, Response
  8 | from pytest_mock.plugin import MockerFixture
  9 | from sqlalchemy import select
 10 | from sqlalchemy.ext.asyncio import AsyncSession
 11 | from sqlalchemy.orm import selectinload
 12 | 
 13 | from src.models import Chat, ChatType, User
 14 | 
 15 | url = "/chat/direct/"
 16 | 
 17 | 
 18 | @pytest.mark.integration
 19 | async def test_integration_post_succeeds_given_no_chat_exists(
 20 |     db_session: AsyncSession, authenticated_bob_client: AsyncClient, bob_user: User, emily_user: User
 21 | ):
 22 |     payload: dict = {"recipient_user_guid": str(emily_user.guid)}
 23 | 
 24 |     response: Response = await authenticated_bob_client.post(url, json=payload)
 25 | 
 26 |     assert response.status_code == status.HTTP_200_OK
 27 |     assert response.json() == {
 28 |         "guid": mock.ANY,
 29 |         "chat_type": "direct",
 30 |         "created_at": mock.ANY,
 31 |         "updated_at": mock.ANY,
 32 |         "users": [
 33 |             {
 34 |                 "guid": mock.ANY,
 35 |                 "first_name": bob_user.first_name,
 36 |                 "last_name": bob_user.last_name,
 37 |                 "username": bob_user.username,
 38 |                 "user_image": None,
 39 |             },
 40 |             {
 41 |                 "guid": mock.ANY,
 42 |                 "first_name": emily_user.first_name,
 43 |                 "last_name": emily_user.last_name,
 44 |                 "username": emily_user.username,
 45 |                 "user_image": None,
 46 |             },
 47 |         ],
 48 |     }
 49 | 
 50 |     # check chat creation in db
 51 |     query = select(Chat).where(Chat.chat_type == ChatType.DIRECT).options(selectinload(Chat.users))
 52 |     result = await db_session.execute(query)
 53 |     chat: Chat | None = result.scalar_one_or_none()
 54 | 
 55 |     assert chat is not None
 56 |     assert chat.chat_type == ChatType.DIRECT
 57 |     assert {bob_user, emily_user} == set(chat.users)
 58 | 
 59 | 
 60 | @pytest.mark.integration
 61 | async def test_post_succeeds_given_no_chat_exists(
 62 |     mocker: MockerFixture, db_session: AsyncSession, authenticated_bob_client: AsyncClient, bob_emily_chat: Chat
 63 | ):
 64 |     payload: dict = {"recipient_user_guid": str(uuid4())}
 65 | 
 66 |     mock_get_user_by_guid: AsyncMock = mocker.patch("src.chat.router.get_user_by_guid")
 67 |     mock_direct_chat_exists: AsyncMock = mocker.patch("src.chat.router.direct_chat_exists", return_value=False)
 68 |     mock_create_direct_chat: AsyncMock = mocker.patch("src.chat.router.create_direct_chat", return_value=bob_emily_chat)
 69 | 
 70 |     response: Response = await authenticated_bob_client.post(url, json=payload)
 71 | 
 72 |     assert response.status_code == status.HTTP_200_OK
 73 |     assert {"guid", "users", "chat_type", "created_at", "updated_at"} == set(response.json().keys())
 74 |     mock_get_user_by_guid.assert_called_once()
 75 |     mock_direct_chat_exists.assert_called_once()
 76 |     mock_create_direct_chat.assert_called_once()
 77 | 
 78 | 
 79 | async def test_post_fails_given_chat_already_exists(
 80 |     mocker: AsyncMock, authenticated_bob_client: AsyncClient, bob_emily_chat: Chat, emily_user: User
 81 | ):
 82 |     recipient_user_guid: str = str(uuid4())
 83 |     payload: dict = {"recipient_user_guid": recipient_user_guid}
 84 |     mock_get_user_by_guid: AsyncMock = mocker.patch("src.chat.router.get_user_by_guid")
 85 |     mock_direct_chat_exists: AsyncMock = mocker.patch("src.chat.router.direct_chat_exists", return_value=True)
 86 |     mock_create_direct_chat: AsyncMock = mocker.patch("src.chat.router.create_direct_chat")
 87 | 
 88 |     response: Response = await authenticated_bob_client.post(url, json=payload)
 89 | 
 90 |     assert response.status_code == status.HTTP_409_CONFLICT
 91 |     assert response.json() == {"detail": f"Chat with recipient user exists [{recipient_user_guid}]"}
 92 |     mock_get_user_by_guid.assert_called_once()
 93 |     mock_direct_chat_exists.assert_called_once()
 94 |     mock_create_direct_chat.assert_not_called()
 95 | 
 96 | 
 97 | async def test_post_fails_given_recipient_does_not_exist(
 98 |     mocker: MockerFixture,
 99 |     authenticated_bob_client: AsyncClient,
100 | ):
101 |     mock_get_user_by_guid: AsyncMock = mocker.patch("src.chat.router.get_user_by_guid", return_value=None)
102 |     mock_direct_chat_exists: AsyncMock = mocker.patch("src.chat.router.direct_chat_exists")
103 |     mock_create_direct_chat: AsyncMock = mocker.patch("src.chat.router.create_direct_chat")
104 |     recipient_user_guid: str = str(uuid4())
105 |     payload: dict = {"recipient_user_guid": recipient_user_guid}
106 | 
107 |     response: Response = await authenticated_bob_client.post(url, json=payload)
108 | 
109 |     assert response.status_code == status.HTTP_404_NOT_FOUND
110 |     assert response.json() == {"detail": f"There is no recipient user with provided guid [{recipient_user_guid}]"}
111 |     mock_get_user_by_guid.assert_called_once()
112 |     mock_direct_chat_exists.assert_not_called()
113 |     mock_create_direct_chat.assert_not_called()
114 | 


--------------------------------------------------------------------------------
/tests/chat/router/test_get_chat_messages.py:
--------------------------------------------------------------------------------
 1 | from unittest import mock
 2 | from uuid import uuid4
 3 | 
 4 | import pytest
 5 | from fastapi import status
 6 | from httpx import AsyncClient, Response
 7 | 
 8 | from src.models import Chat, ReadStatus
 9 | 
10 | 
11 | @pytest.mark.integration
12 | async def test_get_messages_succeeds_given_existing_chat_with_messages(
13 |     authenticated_bob_client: AsyncClient, bob_emily_chat: Chat, bob_emily_chat_messages_history: list[Chat]
14 | ):
15 |     url: str = f"/chat/{bob_emily_chat.guid}/messages/"
16 | 
17 |     response: Response = await authenticated_bob_client.get(url)
18 | 
19 |     assert response.status_code == status.HTTP_200_OK
20 |     assert response.json() == {"messages": mock.ANY, "has_more_messages": False, "last_read_message": None}
21 |     messages = response.json()["messages"]
22 |     assert len(messages) == 20
23 |     assert set(messages[0].keys()) == {
24 |         "message_guid",
25 |         "content",
26 |         "created_at",
27 |         "user_guid",
28 |         "chat_guid",
29 |         "is_read",
30 |     }
31 | 
32 | 
33 | @pytest.mark.integration
34 | async def test_get_messages_succeeds_given_existing_chat_with_messages_and_read_status_for_bob(
35 |     authenticated_bob_client: AsyncClient,
36 |     bob_emily_chat: Chat,
37 |     bob_emily_chat_messages_history: list[Chat],
38 |     bob_read_status: ReadStatus,
39 |     emily_read_status: ReadStatus,
40 | ):
41 |     url: str = f"/chat/{bob_emily_chat.guid}/messages/"
42 | 
43 |     response: Response = await authenticated_bob_client.get(url)
44 | 
45 |     assert response.status_code == status.HTTP_200_OK
46 |     assert response.json() == {"has_more_messages": False, "messages": mock.ANY, "last_read_message": mock.ANY}
47 |     messages = response.json()["messages"]
48 |     assert len(messages) == 20
49 |     assert sum([message["is_read"] for message in messages]) == 15
50 | 
51 | 
52 | @pytest.mark.integration
53 | async def test_get_messages_returns_all_messages_given_no_messages_are_read(
54 |     authenticated_bob_client: AsyncClient, bob_emily_chat: Chat, bob_emily_chat_messages_history: list[Chat]
55 | ):
56 |     url: str = f"/chat/{bob_emily_chat.guid}/messages/"
57 | 
58 |     response: Response = await authenticated_bob_client.get(url, params={"size": 10})
59 | 
60 |     assert response.status_code == status.HTTP_200_OK
61 |     assert response.json() == {"messages": mock.ANY, "has_more_messages": False, "last_read_message": None}
62 |     assert len(response.json()["messages"]) == 20
63 | 
64 | 
65 | @pytest.mark.integration
66 | async def test_get_messages_succeeds_given_existing_chat_without_messages(
67 |     authenticated_bob_client: AsyncClient, bob_emily_chat: Chat
68 | ):
69 |     url: str = f"/chat/{bob_emily_chat.guid}/messages/"
70 | 
71 |     response: Response = await authenticated_bob_client.get(url)
72 | 
73 |     assert response.status_code == status.HTTP_200_OK
74 |     assert response.json() == {"messages": [], "has_more_messages": False, "last_read_message": None}
75 | 
76 | 
77 | @pytest.mark.integration
78 | async def test_get_messages_fails_given_chat_does_not_exist(authenticated_bob_client: AsyncClient):
79 |     url = f"/chat/{uuid4()}/messages/"
80 | 
81 |     response = await authenticated_bob_client.get(url)
82 | 
83 |     assert response.status_code == status.HTTP_404_NOT_FOUND
84 |     assert response.json() == {"detail": "Chat with provided guid does not exist"}
85 | 
86 | 
87 | @pytest.mark.integration
88 | async def test_get_messages_fails_given_user_is_not_chat_participant(
89 |     authenticated_doug_client: AsyncClient, bob_emily_chat: Chat
90 | ):
91 |     url: str = f"/chat/{bob_emily_chat.guid}/messages/"
92 | 
93 |     response: Response = await authenticated_doug_client.get(url)
94 | 
95 |     assert response.status_code == status.HTTP_409_CONFLICT
96 |     assert response.json() == {"detail": "You don't have access to this chat"}
97 | 


--------------------------------------------------------------------------------
/tests/chat/router/test_get_older_messages.py:
--------------------------------------------------------------------------------
 1 | from unittest import mock
 2 | from uuid import uuid4
 3 | 
 4 | from fastapi import status
 5 | from httpx import AsyncClient
 6 | 
 7 | from src.models import Chat, Message
 8 | 
 9 | 
10 | async def test_get_older_messages_succeeds_given_older_messages_exist(
11 |     authenticated_bob_client: AsyncClient, bob_emily_chat: Chat, bob_emily_chat_messages_history: list[Message]
12 | ):
13 |     message_guid = bob_emily_chat_messages_history[-1].guid
14 |     url = f"/chat/{bob_emily_chat.guid}/messages/old/{message_guid}/"
15 | 
16 |     response = await authenticated_bob_client.get(url)
17 | 
18 |     assert response.status_code == status.HTTP_200_OK
19 |     assert response.json() == {"messages": mock.ANY, "has_more_messages": True}
20 |     assert len(response.json()["messages"]) == 10
21 | 
22 | 
23 | async def test_get_older_messages_succeeds_given_no_older_messages(
24 |     authenticated_bob_client: AsyncClient, bob_emily_chat: Chat, bob_emily_chat_messages_history: list[Message]
25 | ):
26 |     message_guid = bob_emily_chat_messages_history[0].guid
27 |     url = f"/chat/{bob_emily_chat.guid}/messages/old/{message_guid}/"
28 | 
29 |     response = await authenticated_bob_client.get(url)
30 | 
31 |     assert response.status_code == status.HTTP_200_OK
32 |     assert response.json() == {"messages": [], "has_more_messages": False}
33 | 
34 | 
35 | async def test_get_older_messages_fails_given_chat_does_not_exist(
36 |     authenticated_bob_client: AsyncClient, bob_emily_chat_messages_history: list[Message]
37 | ):
38 |     unexisting_chat_guid = uuid4()
39 |     message_guid = bob_emily_chat_messages_history[0].guid
40 |     url = f"/chat/{unexisting_chat_guid}/messages/old/{message_guid}/"
41 | 
42 |     response = await authenticated_bob_client.get(url)
43 | 
44 |     assert response.status_code == status.HTTP_404_NOT_FOUND
45 |     assert response.json() == {"detail": "Chat with provided guid is not found"}
46 | 
47 | 
48 | async def test_get_older_messages_fails_given_user_is_not_in_chat(
49 |     authenticated_doug_client: AsyncClient, bob_emily_chat: Chat, bob_emily_chat_messages_history: list[Message]
50 | ):
51 |     message_guid = bob_emily_chat_messages_history[0].guid
52 |     url = f"/chat/{bob_emily_chat.guid}/messages/old/{message_guid}/"
53 | 
54 |     response = await authenticated_doug_client.get(url)
55 | 
56 |     assert response.status_code == status.HTTP_409_CONFLICT
57 |     assert response.json() == {"detail": "You don't have access to this chat"}
58 | 
59 | 
60 | async def test_get_older_messages_fails_given_no_message_with_provided_guid(
61 |     authenticated_bob_client: AsyncClient, bob_emily_chat: Chat, bob_emily_chat_messages_history: list[Message]
62 | ):
63 |     unexisting_message_guid = uuid4()
64 |     url = f"/chat/{bob_emily_chat.guid}/messages/old/{unexisting_message_guid}/"
65 | 
66 |     response = await authenticated_bob_client.get(url)
67 | 
68 |     assert response.status_code == status.HTTP_404_NOT_FOUND
69 |     assert response.json() == {"detail": "Message with provided guid is not found"}
70 | 


--------------------------------------------------------------------------------
/tests/chat/router/test_get_user_chats.py:
--------------------------------------------------------------------------------
 1 | from unittest import mock
 2 | 
 3 | from fastapi import status
 4 | from httpx import AsyncClient
 5 | 
 6 | from src.models import Chat
 7 | 
 8 | 
 9 | async def test_get_user_chats_succeeds_given_chat_exists(authenticated_bob_client: AsyncClient, bob_emily_chat: Chat):
10 |     response = await authenticated_bob_client.get("/chats/direct/")
11 | 
12 |     assert response.status_code == status.HTTP_200_OK
13 |     assert response.json() == {"chats": mock.ANY, "total_unread_messages_count": 0}
14 | 
15 |     chats = response.json()["chats"]
16 |     assert len(chats) == 1
17 |     assert set(chats[0].keys()) == {
18 |         "chat_guid",
19 |         "chat_type",
20 |         "created_at",
21 |         "updated_at",
22 |         "users",
23 |         "new_messages_count",
24 |     }
25 | 
26 | 
27 | async def test_get_user_chats_succeeds_given_user_has_multiple_chats(
28 |     authenticated_bob_client: AsyncClient, bob_emily_chat: Chat, bob_doug_chat: Chat
29 | ):
30 |     response = await authenticated_bob_client.get("/chats/direct/")
31 | 
32 |     assert response.status_code == status.HTTP_200_OK
33 |     assert response.json() == {"chats": mock.ANY, "total_unread_messages_count": 0}
34 | 
35 |     chats = response.json()["chats"]
36 |     assert len(chats) == 2
37 |     for chat in chats:
38 |         assert set(chat.keys()) == {
39 |             "chat_guid",
40 |             "chat_type",
41 |             "created_at",
42 |             "updated_at",
43 |             "users",
44 |             "new_messages_count",
45 |         }
46 | 


--------------------------------------------------------------------------------
/tests/chat/services/test_direct_chat_exists.py:
--------------------------------------------------------------------------------
 1 | from sqlalchemy.ext.asyncio import AsyncSession
 2 | 
 3 | from src.chat.services import direct_chat_exists
 4 | from src.models import Chat, User
 5 | 
 6 | 
 7 | async def test_direct_chat_exists_returns_true_given_chat_exists(
 8 |     db_session: AsyncSession,
 9 |     bob_doug_chat: Chat,
10 |     bob_emily_chat: Chat,
11 |     bob_user: User,
12 |     emily_user: User,
13 | ):
14 |     assert await direct_chat_exists(db_session, current_user=bob_user, recipient_user=emily_user)
15 | 
16 | 
17 | async def test_direct_chat_exists_returns_false_given_chat_does_not_exist(
18 |     db_session: AsyncSession,
19 |     bob_doug_chat: Chat,
20 |     bob_user: User,
21 |     emily_user: User,
22 | ):
23 |     assert not await direct_chat_exists(db_session, current_user=bob_user, recipient_user=emily_user)
24 | 


--------------------------------------------------------------------------------
/tests/conftest.py:
--------------------------------------------------------------------------------
  1 | import asyncio
  2 | from datetime import datetime
  3 | from typing import AsyncGenerator
  4 | 
  5 | import pytest
  6 | from asgi_lifespan import LifespanManager
  7 | from fastapi.testclient import TestClient
  8 | from httpx import AsyncClient
  9 | from sqlalchemy import text
 10 | from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine
 11 | from sqlalchemy.orm import sessionmaker
 12 | 
 13 | from src.authentication.utils import create_access_token
 14 | from src.config import settings
 15 | from src.database import get_async_session, metadata
 16 | from src.main import app
 17 | from src.models import Chat, ChatType, Message, ReadStatus, User
 18 | from src.utils import get_hashed_password
 19 | 
 20 | DATABASE_URL_TEST = (
 21 |     f"postgresql+asyncpg://"
 22 |     f"{settings.DB_USER}:{settings.DB_PASSWORD}@{settings.DB_HOST}:{settings.DB_PORT}/{settings.DB_NAME}"
 23 | )
 24 | engine_test = create_async_engine(DATABASE_URL_TEST, connect_args={"server_settings": {"jit": "off"}})
 25 | autocommit_engine = engine_test.execution_options(isolation_level="AUTOCOMMIT")
 26 | 
 27 | async_session_maker = sessionmaker(autocommit_engine, class_=AsyncSession, expire_on_commit=False)
 28 | metadata.bind = engine_test
 29 | 
 30 | 
 31 | async def override_get_async_session() -> AsyncGenerator[AsyncSession, None]:
 32 |     async with async_session_maker() as session:
 33 |         yield session
 34 | 
 35 | 
 36 | app.dependency_overrides[get_async_session] = override_get_async_session
 37 | 
 38 | 
 39 | @pytest.fixture
 40 | async def db_session():
 41 |     async with autocommit_engine.begin() as conn:
 42 |         await conn.execute(text(f"CREATE SCHEMA IF NOT EXISTS {settings.DB_SCHEMA}"))
 43 |         await conn.run_sync(metadata.create_all)
 44 |     async with async_session_maker() as session:
 45 |         yield session
 46 |         await session.flush()
 47 |         await session.rollback()
 48 |     async with autocommit_engine.begin() as conn:
 49 |         await conn.execute(text(f"DROP SCHEMA {settings.DB_SCHEMA} CASCADE"))
 50 | 
 51 | 
 52 | # https://stackoverflow.com/questions/4763472/sqlalchemy-clear-database-content-but-dont-drop-the-schema
 53 | @pytest.fixture(autouse=True)
 54 | async def clear_tables(db_session: AsyncSession):
 55 |     for table in reversed(metadata.sorted_tables):
 56 |         await db_session.execute(table.delete())
 57 |     await db_session.commit()
 58 | 
 59 | 
 60 | client = TestClient(app)
 61 | 
 62 | 
 63 | @pytest.fixture(scope="session")
 64 | def event_loop(request):
 65 |     """Create an instance of the default event loop for each test case."""
 66 |     loop = asyncio.get_event_loop_policy().new_event_loop()
 67 |     yield loop
 68 |     loop.close()
 69 | 
 70 | 
 71 | @pytest.fixture
 72 | async def async_client() -> AsyncGenerator[AsyncClient, None]:
 73 |     # httpx does not implement lifespan protocol and trigger startup event handlers.
 74 |     # For this, you need to use LifespanManager.
 75 |     # https://stackoverflow.com/questions/65051581/how-to-trigger-lifespan-startup-and-shutdown-while-testing-fastapi-app
 76 |     async with LifespanManager(app):
 77 |         async with AsyncClient(app=app, base_url="http://test") as async_client:
 78 |             yield async_client
 79 | 
 80 | 
 81 | @pytest.fixture
 82 | async def bob_user(db_session: AsyncSession) -> User:
 83 |     user = User(
 84 |         password=get_hashed_password("password"),
 85 |         username="bob_username",
 86 |         first_name="Bob",
 87 |         last_name="Stewart",
 88 |         email="user@example.com",
 89 |     )
 90 |     db_session.add(user)
 91 |     await db_session.commit()
 92 | 
 93 |     return user
 94 | 
 95 | 
 96 | @pytest.fixture
 97 | def authenticated_bob_client(async_client: AsyncClient, bob_user: User):
 98 |     access_token = create_access_token(subject=bob_user.email)
 99 |     async_client.cookies.update({"access_token": access_token})
100 | 
101 |     yield async_client
102 | 
103 | 
104 | @pytest.fixture
105 | async def emily_user(db_session: AsyncSession) -> User:
106 |     user = User(
107 |         password=get_hashed_password("password"),
108 |         username="emily",
109 |         first_name="Emily",
110 |         last_name="Hazel",
111 |         email="emily@example.com",
112 |     )
113 |     db_session.add(user)
114 |     await db_session.commit()
115 | 
116 |     return user
117 | 
118 | 
119 | @pytest.fixture
120 | async def doug_user(db_session: AsyncSession) -> User:
121 |     user = User(
122 |         password=get_hashed_password("password"),
123 |         username="douglas",
124 |         first_name="Douglas",
125 |         last_name="Walrus",
126 |         email="douglas@example.com",
127 |     )
128 |     db_session.add(user)
129 |     await db_session.commit()
130 | 
131 |     return user
132 | 
133 | 
134 | @pytest.fixture
135 | def authenticated_doug_client(async_client: AsyncClient, doug_user: User):
136 |     access_token = create_access_token(subject=doug_user.email)
137 |     async_client.cookies.update({"access_token": access_token})
138 | 
139 |     yield async_client
140 | 
141 | 
142 | @pytest.fixture
143 | async def bob_emily_chat(db_session: AsyncSession, bob_user: User, emily_user: User) -> Chat:
144 |     chat = Chat(chat_type=ChatType.DIRECT)
145 |     chat.users.append(bob_user)
146 |     chat.users.append(emily_user)
147 |     db_session.add(chat)
148 |     await db_session.flush()
149 |     # make empty read statuses for both users last_read_message_id = 0
150 |     initiator_read_status = ReadStatus(chat_id=chat.id, user_id=bob_user.id, last_read_message_id=0)
151 |     recipient_read_status = ReadStatus(chat_id=chat.id, user_id=emily_user.id, last_read_message_id=0)
152 |     db_session.add_all([initiator_read_status, recipient_read_status])
153 |     await db_session.commit()
154 | 
155 |     return chat
156 | 
157 | 
158 | @pytest.fixture
159 | async def bob_doug_chat(db_session: AsyncSession, doug_user: User, bob_user: User) -> Chat:
160 |     chat = Chat(chat_type=ChatType.DIRECT)
161 |     chat.users.append(doug_user)
162 |     chat.users.append(bob_user)
163 |     db_session.add(chat)
164 |     await db_session.flush()
165 |     # make empty read statuses for both users last_read_message_id = 0
166 |     initiator_read_status = ReadStatus(chat_id=chat.id, user_id=bob_user.id, last_read_message_id=0)
167 |     recipient_read_status = ReadStatus(chat_id=chat.id, user_id=doug_user.id, last_read_message_id=0)
168 |     db_session.add_all([initiator_read_status, recipient_read_status])
169 | 
170 |     await db_session.commit()
171 | 
172 |     return chat
173 | 
174 | 
175 | @pytest.fixture
176 | async def bob_emily_chat_messages_history(
177 |     db_session: AsyncSession, bob_user: User, emily_user: User, bob_emily_chat: Chat
178 | ) -> list[Message]:
179 |     await db_session.refresh(bob_emily_chat, attribute_names=["messages"])
180 |     Bob = True
181 |     sender_id = bob_user.id if Bob else emily_user.id
182 | 
183 |     for i in range(1, 21):
184 |         sender_name = "Bob" if Bob else "Emily"
185 |         message = Message(
186 |             content=f"#{i} message sent by {sender_name}",
187 |             user_id=sender_id,
188 |             chat_id=bob_emily_chat.id,
189 |             created_at=datetime.now(),
190 |         )
191 |         Bob = not Bob
192 |         bob_emily_chat.messages.append(message)
193 | 
194 |     db_session.add(bob_emily_chat)
195 |     await db_session.commit()
196 | 
197 |     return bob_emily_chat.messages
198 | 
199 | 
200 | @pytest.fixture
201 | async def bob_read_status(
202 |     db_session: AsyncSession,
203 |     bob_user: User,
204 |     bob_emily_chat_messages_history: list[Message],
205 | ) -> ReadStatus:
206 |     # bob read 10 messages
207 |     last_read_message = bob_emily_chat_messages_history[9]
208 |     read_status = ReadStatus(
209 |         user_id=bob_user.id,
210 |         chat_id=last_read_message.chat.id,
211 |         last_read_message_id=last_read_message.id,
212 |     )
213 |     db_session.add(read_status)
214 |     await db_session.commit()
215 | 
216 |     return read_status
217 | 
218 | 
219 | @pytest.fixture
220 | async def emily_read_status(
221 |     db_session: AsyncSession,
222 |     emily_user: User,
223 |     bob_emily_chat_messages_history: list[Message],
224 | ) -> ReadStatus:
225 |     # emily read 15 messages
226 |     last_read_message = bob_emily_chat_messages_history[14]
227 |     read_status = ReadStatus(
228 |         user_id=emily_user.id,
229 |         chat_id=last_read_message.chat.id,
230 |         last_read_message_id=last_read_message.id,
231 |     )
232 |     db_session.add(read_status)
233 |     await db_session.commit()
234 | 
235 |     return read_status
236 | 


--------------------------------------------------------------------------------
/tests/contact/test_contact_router.py:
--------------------------------------------------------------------------------
 1 | from unittest import mock
 2 | 
 3 | from fastapi import status
 4 | from httpx import AsyncClient, Response
 5 | from sqlalchemy.ext.asyncio import AsyncSession
 6 | 
 7 | from src.models import User
 8 | 
 9 | 
10 | async def test_get_contacts_returns_empty_list_given_only_current_user(
11 |     db_session: AsyncSession, authenticated_bob_client: AsyncClient, bob_user: User
12 | ):
13 |     response: Response = await authenticated_bob_client.get("/users/")
14 | 
15 |     assert response.status_code == status.HTTP_200_OK
16 |     assert response.json() == []
17 | 
18 | 
19 | async def test_get_contacts_returns_users_list_given_multiple_other_users(
20 |     mocker,
21 |     db_session: AsyncSession,
22 |     authenticated_bob_client: AsyncClient,
23 |     bob_user: User,
24 |     emily_user: User,
25 |     doug_user: User,
26 | ):
27 |     response: Response = await authenticated_bob_client.get("/users/")
28 | 
29 |     assert response.status_code == status.HTTP_200_OK
30 |     assert response.json() == [
31 |         {
32 |             "guid": mock.ANY,
33 |             "username": "douglas",
34 |             "email": "douglas@example.com",
35 |             "first_name": "Douglas",
36 |             "last_name": "Walrus",
37 |             "created_at": mock.ANY,
38 |             "user_image": None,
39 |         },
40 |         {
41 |             "guid": mock.ANY,
42 |             "username": "emily",
43 |             "email": "emily@example.com",
44 |             "first_name": "Emily",
45 |             "last_name": "Hazel",
46 |             "created_at": mock.ANY,
47 |             "user_image": None,
48 |         },
49 |     ]
50 | 


--------------------------------------------------------------------------------
/tests/registration/test_registration_router.py:
--------------------------------------------------------------------------------
 1 | from fastapi import status
 2 | from httpx import AsyncClient
 3 | from sqlalchemy import select
 4 | from sqlalchemy.ext.asyncio import AsyncSession
 5 | 
 6 | from src.models import User
 7 | from src.utils import verify_password
 8 | 
 9 | 
10 | async def test_register_succeeds_given_valid_data(db_session: AsyncSession, async_client: AsyncClient):
11 |     url = "/register/"
12 |     payload = {
13 |         "email": "ExamPle@gmail.com",
14 |         "username": "bOb_userName",
15 |         "password": "test_password",
16 |         "first_name": "John",
17 |         "last_name": "Doe",
18 |     }
19 |     response = await async_client.post(url, data=payload)
20 | 
21 |     assert response.status_code == status.HTTP_200_OK
22 |     assert response.json() == "User has been successfully created"
23 | 
24 |     # confirm changes in db
25 |     statement = select(User).where(User.email == "example@gmail.com")
26 |     result = await db_session.execute(statement)
27 | 
28 |     user: User = result.scalar_one_or_none()
29 | 
30 |     assert user is not None
31 |     assert user.username == "bob_username"
32 |     assert user.first_name == "John"
33 |     assert user.last_name == "Doe"
34 |     assert user.email == "example@gmail.com"
35 |     assert verify_password("test_password", user.password) is True
36 | 
37 | 
38 | async def test_register_fails_given_invalid_email(async_client: AsyncClient):
39 |     url = "/register/"
40 |     payload = {
41 |         "email": "not_valid_email.com",
42 |         "username": "bob_username",
43 |         "password": "test_password",
44 |         "first_name": "John",
45 |         "last_name": "Doe",
46 |     }
47 | 
48 |     response = await async_client.post(url, data=payload)
49 | 
50 |     assert response.status_code == status.HTTP_422_UNPROCESSABLE_ENTITY
51 |     assert "value is not a valid email address" in response.json()["detail"][0]["msg"]
52 | 
53 | 
54 | async def test_register_fails_given_user_with_provided_email_already_exists(async_client: AsyncClient, bob_user: User):
55 |     url = "/register/"
56 |     payload = {
57 |         "email": bob_user.email,
58 |         "username": "bob_username",
59 |         "password": "test_password",
60 |         "first_name": "John",
61 |         "last_name": "Doe",
62 |     }
63 | 
64 |     response = await async_client.post(url, data=payload)
65 | 
66 |     assert response.status_code == status.HTTP_409_CONFLICT
67 |     assert response.json() == {"detail": "User with provided credentials already exists"}
68 | 
69 | 
70 | async def test_register_fails_given_user_with_provided_username_already_exists(
71 |     async_client: AsyncClient, bob_user: User
72 | ):
73 |     url = "/register/"
74 |     payload = {
75 |         "email": "example2@email.com",
76 |         "username": bob_user.username,
77 |         "password": "test_password",
78 |         "first_name": "John",
79 |         "last_name": "Doe",
80 |     }
81 | 
82 |     response = await async_client.post(url, data=payload)
83 | 
84 |     assert response.status_code == status.HTTP_409_CONFLICT
85 |     assert response.json() == {"detail": "User with provided credentials already exists"}
86 | 


--------------------------------------------------------------------------------
/tests/settings/test_settings_router.py:
--------------------------------------------------------------------------------
 1 | from fastapi import status
 2 | from httpx import AsyncClient, Response
 3 | from sqlalchemy.ext.asyncio import AsyncSession
 4 | 
 5 | from src.models import User
 6 | 
 7 | 
 8 | async def test_set_user_theme_succeeds_given_theme_exists(
 9 |     authenticated_bob_client: AsyncClient, bob_user: User, db_session: AsyncSession
10 | ):
11 |     response: Response = await authenticated_bob_client.post("/user/settings/theme/", json={"theme": "teal"})
12 |     await db_session.refresh(bob_user)
13 | 
14 |     assert response.status_code == status.HTTP_200_OK
15 |     assert response.json() == {"message": "New theme has been set"}
16 |     assert bob_user.settings == {"theme": "teal"}
17 | 
18 | 
19 | async def test_set_user_theme_fails_given_theme_does_not_exist(
20 |     authenticated_bob_client: AsyncClient,
21 | ):
22 |     response: Response = await authenticated_bob_client.post("/user/settings/theme/", json={"theme": "Unknown"})
23 | 
24 |     assert response.status_code == status.HTTP_422_UNPROCESSABLE_ENTITY
25 |     assert response.json()["detail"][0]["msg"] == "Input should be 'teal' or 'midnight'"
26 | 


--------------------------------------------------------------------------------
