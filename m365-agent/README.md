# FinOps CCoE Agent for Microsoft 365 Copilot

This folder packages the existing live FinOps API as a Microsoft 365 declarative agent with a read-only API plugin.

## Architecture

1. Microsoft 365 Copilot hosts the declarative agent and language model.
2. The API plugin invokes the existing FastAPI application.
3. The plugin uses delegated Microsoft Entra OAuth for Azure Resource Manager.
4. FastAPI forwards the user's Azure token to live Azure APIs.
5. The user's tenant membership and Azure RBAC remain the authorization boundary.

The agent does not have write actions and does not use application-wide Azure permissions.

## Tenant prerequisites

Use the `wolffofficetenant` Microsoft 365 tenant and confirm:

1. The publishing account has Microsoft 365 Copilot access.
2. Custom app upload is enabled.
3. The Microsoft 365 Agents Toolkit is signed into that tenant.
4. An Azure subscription in that tenant is selected for the FastAPI deployment.

The Azure extensions context used while creating this package remained signed into the Microsoft corporate tenant. Sign out and sign in with the `wolffofficetenant` Azure/Microsoft 365 account before provisioning or publishing.

## Configure authentication

Create an OAuth authentication configuration with Microsoft 365 Agents Toolkit or the Teams Developer Portal:

1. Register a single-tenant application in `wolffofficetenant`.
2. Add the redirect URI shown by the authentication configuration workflow.
3. Use the authorization URL `https://login.microsoftonline.com/<tenant-id>/oauth2/v2.0/authorize`.
4. Use the token URL `https://login.microsoftonline.com/<tenant-id>/oauth2/v2.0/token`.
5. Use scope `https://management.azure.com/user_impersonation offline_access`.
6. Record the generated OAuth authentication configuration reference ID.

Do not commit the client secret or OAuth reference to this repository.

## Build the app package

Deploy the FastAPI app to an HTTPS endpoint, then run:

```powershell
.\m365-agent\build-package.ps1 `
  -ApiBaseUrl 'https://<app-name>.azurewebsites.net' `
  -OAuthReferenceId '<oauth-auth-config-reference-id>' `
  -TenantIdOrDomain '<wolffofficetenant-tenant-id>' `
  -ContactEmail '<support-email>'
```

The command creates `m365-agent/dist/finops-ccoe-agent.zip`.

## Validate and publish

1. Sign into Microsoft 365 Agents Toolkit with the `wolffofficetenant` account.
2. Validate `m365-agent/dist/finops-ccoe-agent.zip`.
3. Sideload it to test in Microsoft 365 Copilot.
4. Verify each conversation starter and inspect source timestamps, warnings, and RBAC behavior.
5. Submit the package to the organizational catalog for admin approval.

For marketplace publication, also complete Microsoft Responsible AI validation, agent store validation, privacy/support review, and Partner Center submission.