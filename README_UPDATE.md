PIC Project improved notes

What changed
1) Replaced pic_project/bridge.py with a working Flask app that stores messages in chat_data/messages.json using atomic writes and CORS enabled.
2) Replaced pic_project/utils/message_handler.py with stable helpers: read_messages, append_message, create_message, clear_messages.
3) Replaced pic_project/run.py with a small dispatcher that starts the bridge on 127.0.0.1:5173 and routes user messages to agents. Pain agent is wired up.
4) Replaced pic_akatsuki/app.js with a minimal but working client that polls /messages and posts to /send. It supports toggling a DM target by clicking an agent in the sidebar.
5) Added requirements.txt (Flask and flask-cors).

How to run
1) pip install -r requirements.txt
2) python -m pic_project.run
3) Open pic_akatsuki/index.html in a browser. The UI will poll the bridge on http://127.0.0.1:5173.

Quick test
1) Type: /status as a DM to Pain (click Pain in the left panel to activate DM). Pain should reply.
2) Type a broadcast like: Clip summary: Elden Ring, Maliketh fight, clutch heal and riposte at 2m10s. Pain will broadcast a task sheet.

Packaging
Use PyInstaller to make an exe:
pyinstaller -F -n pic_bridge pic_project/bridge.py

Then run the exe and point PIC_CHAT_DIR to a writable folder if needed.

New features

1) Live updates using SSE
- GET /stream streams events with id, event type, and JSON payload. UI connects via EventSource and falls back to polling if needed.
- Bridge bumps a version number when messages change and pushes updates.

2) JSON schema validation
- Schema file at pic_project/schema/message.schema.json.
- /send validates requests and returns 400 on schema_validation_failed with details.

3) New agents
- Konan handles /captions and auto responds to a PIC Task Sheet with a caption plan.
- Deidara handles /thumb and auto responds to a PIC Task Sheet with 3 thumbnail prompt variants.
- agents.REGISTRY includes Pain, Konan, and Deidara.

4) PyInstaller specs
- buildspec/pic_bridge.spec builds a pic_bridge.exe running the Flask bridge.
- buildspec/pic_runner.spec builds a pic_runner.exe that starts bridge and dispatcher.

5) GitHub Actions CI
- .github/workflows/build.yml builds Windows exe on every push and uploads artifacts.
- On branch pushes, it creates a prerelease with tag build-RUN_NUMBER.
- On version tags v*, it publishes a normal release.

Run notes
- Start the system: python -m pic_project.run, then open pic_akatsuki/index.html.
- DM Konan: /captions hook a dodge roll then riposte.
- DM Deidara: /thumb maliketh riposte.
