# Example input

A typical run. The user has just finished a feature branch and wants a PR description before opening the PR.

---

**User:**

> Write a PR description for this branch. Diff is below.
>
> ```diff
> diff --git a/src/auth/device.ts b/src/auth/device.ts
> new file mode 100644
> --- /dev/null
> +++ b/src/auth/device.ts
> @@ -0,0 +1,84 @@
> +import { randomBytes } from "crypto";
> +import { db } from "../db";
> +import { issueToken } from "./issuer";
> +
> +const DEVICE_CODE_TTL_MS = 15 * 60 * 1000;
> +const POLL_BACKOFF_MS = 5000;
> +
> +export async function startDeviceFlow(clientId: string) {
> +  const deviceCode = randomBytes(32).toString("hex");
> +  const userCode = randomBytes(4).toString("hex").toUpperCase();
> +  const expiresAt = new Date(Date.now() + DEVICE_CODE_TTL_MS);
> +
> +  await db.deviceCode.create({
> +    data: { deviceCode, userCode, clientId, expiresAt },
> +  });
> +
> +  return {
> +    deviceCode,
> +    userCode,
> +    verificationUri: "https://auth.example.com/device",
> +    expiresIn: DEVICE_CODE_TTL_MS / 1000,
> +    interval: POLL_BACKOFF_MS / 1000,
> +  };
> +}
> +
> +export async function pollDeviceFlow(deviceCode: string) {
> +  const record = await db.deviceCode.findUnique({ where: { deviceCode } });
> +  if (!record) return { error: "invalid_code" };
> +  if (record.expiresAt < new Date()) return { error: "expired" };
> +  if (!record.userId) return { error: "authorization_pending" };
> +  return { token: await issueToken(record.userId) };
> +}
>
> diff --git a/src/auth/routes.ts b/src/auth/routes.ts
> --- a/src/auth/routes.ts
> +++ b/src/auth/routes.ts
> @@ -12,6 +12,7 @@ import { passwordRoutes } from "./password";
>  import { ssoRoutes } from "./sso";
> +import { deviceRoutes } from "./device-routes";
>
>  export function authRoutes(app: Express) {
>    app.use("/auth", passwordRoutes);
>    app.use("/auth", ssoRoutes);
> +  app.use("/auth", deviceRoutes);
>  }
>
> diff --git a/src/cli/login.ts b/src/cli/login.ts
> --- a/src/cli/login.ts
> +++ b/src/cli/login.ts
> @@ -3,6 +3,7 @@ import { Command } from "commander";
>
>  export const loginCommand = new Command("login")
>    .option("--password", "use password auth")
>    .option("--sso", "use SSO auth")
> +  .option("--device", "use device-flow auth (no browser required)")
>    .action(async (opts) => {
>      // ... existing handlers ...
> +    if (opts.device) await deviceFlow();
>    });
>
> diff --git a/prisma/migrations/20260522_add_device_codes/migration.sql b/prisma/migrations/20260522_add_device_codes/migration.sql
> new file mode 100644
> --- /dev/null
> +++ b/prisma/migrations/20260522_add_device_codes/migration.sql
> @@ -0,0 +1,9 @@
> +CREATE TABLE "DeviceCode" (
> +    "deviceCode" TEXT NOT NULL,
> +    "userCode" TEXT NOT NULL,
> +    "clientId" TEXT NOT NULL,
> +    "userId" TEXT,
> +    "expiresAt" TIMESTAMP(3) NOT NULL,
> +    "createdAt" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
> +    CONSTRAINT "DeviceCode_pkey" PRIMARY KEY ("deviceCode")
> +);
>
> diff --git a/tests/auth/device.test.ts b/tests/auth/device.test.ts
> new file mode 100644
> --- /dev/null
> +++ b/tests/auth/device.test.ts
> @@ -0,0 +1,42 @@
> +// ... 42 lines of tests for startDeviceFlow, pollDeviceFlow, expiry, backoff ...
> ```
>
> Commit log for the same range:
>
> ```
> a1b2c3d feat(auth): add device flow startup
> e4f5g6h feat(auth): add device flow polling and expiry
> i7j8k9l test(auth): cover device flow happy path + expiry
> m0n1o2p chore(auth): wire device routes into the auth router
> q3r4s5t feat(cli): add --device option to login command
> ```

---

## Skill reads + analyzes

- Detects this is a **feature** + **schema change** + **auth change**
- Notes the migration file → flag as risk
- Notes the auth routes change → flag as risk
- Notes new env var → none found, no flag
- Detects Conventional Commits style from the commit log → uses for title
- Detects this is a service (Express + Prisma) → ops-aware tone
