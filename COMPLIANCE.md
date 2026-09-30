# Compliance Notes

This document records the current legal posture of MoneyPrinterTurbo with
respect to EU platform-regulation frameworks (Digital Services Act, the
German UrhDaG implementing Art. 17 of the Copyright in the Digital Single
Market Directive, and the AI Act), and what would need to change if the
tool's deployment model ever shifted.

## Status quo: a local, single-user tool

MoneyPrinterTurbo runs as a local application. Its Docker Compose setups
bind the web UI and API exclusively to the loopback interface:

- `docker-compose.yml:11` — `"127.0.0.1:8501:8501"` (WebUI)
- `docker-compose.yml:21` — `"127.0.0.1:8080:8080"` (API)
- `docker-compose.release.yml:10` / `:18` — same `127.0.0.1` bindings

There is no authentication or session/user model in the API layer
(`app/controllers/v1/video.py`, `app/controllers/v1/llm.py`); the only
credential handled there is the internal Redis password, unrelated to
end-user accounts. In other words: one operator, running the tool on their
own machine, for their own output.

## Why DSA / UrhDaG / Art. 17 do not currently apply

The DSA's hosting-provider liability rules (Art. 6), the UrhDaG's
platform-copyright-liability regime (implementing Art. 17 CDSM — licensing
best-efforts, notice-and-blocking, a 7-day complaint procedure, and
"presumably permitted" thresholds for minor uses such as ≤15s of
video/audio, ≤160 characters of text, or ≤125KB of images), and equivalent
platform-operator obligations all presuppose that a service makes content
available **to other users or to the public**.

MoneyPrinterTurbo does not do this. The operator supplies their own
ingredients (voice, music, footage) for their own video, and any publishing
to TikTok, Instagram, or YouTube Shorts (see the cross-platform publishing
feature) is the operator uploading their own result to their own external
accounts — the application itself never hosts or redistributes content to
third parties. There is accordingly no rightsholder-facing complaint queue,
takedown mechanism, or bagatelle-threshold check in the code, because none
is currently required.

## Stock media licensing

Stock footage sourcing is documented in the feature list
(`README.md:130`, `README-en.md:131`): local assets or free-to-use HD
footage from Pexels, Pixabay, and Coverr. This document does not duplicate
that licensing information — see those sources' own terms for the media
they provide.

## AI Act: generative output

Video scripts, voiceovers, and edits are generated per the operator's own
prompts and settings. Responsibility for content published externally rests
with the operator who publishes it, not with this application acting as a
hosting or deployment platform — the AI Act's deployer obligations for
generative systems attach to whoever operates a service that makes
AI-generated content available to others, which, per the status quo above,
this tool does not do.

## If this ever changes

If MoneyPrinterTurbo were ever redeployed as a multi-tenant hosted service
(shared instance, public-facing API, or a mode where one operator's output
becomes visible to other users), the obligations above would attach and the
following would need to be built before that launch, not after:

- A notice-and-takedown intake for rightsholder complaints, with the
  UrhDaG's 7-day human-review turnaround.
- An automated or manual check against the UrhDaG bagatelle thresholds
  (duration/character/size limits) before treating a use as "presumably
  permitted."
- User-facing terms clarifying deployer responsibility for AI-generated
  output under the AI Act.
- A DSA-compliant point of contact and reporting mechanism.

None of the above is implemented, and none is needed for the tool as it
ships today.
