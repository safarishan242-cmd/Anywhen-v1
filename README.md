# Anywhen — Domain Deployment Edition

This is the full Anywhen v2 application, packaged with a Render Blueprint for deployment to **https://anywhenai.online**. It includes the server, frontend, tests, and secure environment-variable hooks for the official Claude API.

**Start here:** [`DEPLOY_ANYWHEN_ONLINE.md`](./DEPLOY_ANYWHEN_ONLINE.md).

The `render.yaml` requires a paid Render instance and a persistent disk. No Claude API key is included. The backend has been adjusted so Render health checks don't create unnecessary guest sessions and the app accepts browser requests from the exact Render temporary domain while DNS is being set up.

## Local development

Copy `.env.example` to `.env`, add a **new rotated key**, and run:

```bash
npm install
npm start
```

Then visit `http://localhost:3456`. Run the test suite with `npm test`.
