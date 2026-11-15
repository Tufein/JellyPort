# Pull request checklist

- [ ] `python3 -m unittest discover -s tests` passes
- [ ] `node --test tests/*.cjs` and `node --check plexport/static/app.js` pass
- [ ] Website flows checked on desktop **and** a narrow mobile viewport, including dark mode, errors
      and cancellations (CI runs the same three commands plus a Docker build)
- [ ] `CHANGELOG.md` and, if behaviour changed, `docs/TECHNISCH.md` / `README.md` updated
- [ ] No real NAS, downloader or Plex data touched: tests use temporary folders and local fixtures
- [ ] Anything not exercised is listed as **not verified** here, not implied
      (`android/VALIDATIE.md` for the native app; see [SECURITY.md](../SECURITY.md) for credentials)
