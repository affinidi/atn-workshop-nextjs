## Affinidi Credential Issuance Service

Credential Issuance Service provides applications with secure methods of issuing and claiming credentials. It implements the [OID4VCI](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html) (OpenID for Verifiable Credential Issuance) protocol, which provides the mechanism for Issuers to issue Verifiable Credentials to Affinidi Vault users and obtain the credentials using the OAuth 2.0 authorisation flow.

More Details on Affinidi Credential Issuance Service is available on [Affinidi Documentation](https://docs.affinidi.com/docs/affinidi-elements/credential-issuance/)

## Introduction

We will use the same [Next.js](https://nextjs.org/) app that we used in module 1 and enable Affinidi Credentials Issuance Service in the `credentials` page with `nextjs` Framework. we will dive into the Credentials Issuance feature to issue tamper-evident digital credentials, enabling trust in digital interactions through the flow of portable trusted data.

## Architecture

When issuing a Verifiable Credential, three main flows happen within the whole process:

- Issuance Configuration

![Issuance Configuration](/images/cis-config.png)

- Credential Issuance Flow

![Credential Issuance Flow](/images/cis-flow.png)

- Credential Offer Claim [ default : **TX_CODE**]

![Credential Offer Claim](/images/cis-claim.png)

## What you will build?

![Affinidi Credential Issuance](/images/cis.gif)

## Table of content

| Content                        | Description                                                                                                                                                          |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Pre-Requisite`                | Complete the [pre-requisite](#pre-requisite) for Affinidi Credential Issuance Service                                                                                |
| `install dependencies`         | Install [dependencis](#install-dependencies)                                                                                                                         |
| `create files & folders`       | Create required directories and files in the [app](#create-required-directories-and-files)                                                                           |
| ` personal access token`       | Create [personal access token (PAT)](#create-personal-access-token-pat)                                                                                              |
| `credential issuance config`   | Configure [Credential Issuance Configuration](#configure-credential-issuance-configuration)                                                                          |
| `update .env files`            | Update [.env](#update-environment-variables) files                                                                                                                   |
| `Create frontend Env reader`   | Create frontend env reader file [variables.ts](#create-frontend-env-reader) files                                                                                    |
| `update backend Env reader`    | Update backend env reader file [env.ts](#update-backend-env-reader)                                                                                                  |
| `create routes, form & button` | Create [`/credentials`](#create-credentials-page-with-form-and-button-with-default-vaules-and-session) page with `form` and `button` with default values and session |
| `create handleSubmit function` | Create [handleSubmit](#apply-action-on-handlesubmit-function) function which invoke the Affinidi Credentials Issuance Service.                                       |
| `frontend API for issuance`    | Create Front-end API [issuance-start.ts](#create-frontend-api-used-in-handlesubmit-function-to-pass-details-to-backend-api)                                          |
| `backend API for issuance`     | Create Back-end API [credentials-client.ts](#create-backend-api-to-call-affinidi-tdk-for-affinidi-auth-provider-and-affinidi-credentials-issuance-client)            |
| `Run Application`              | Try the App with [Affinidi Login & Affinidi Credentials Issuance Configuration](#run-the-application-to-experience-affinidi-credentials-issuance)                    |

## Pre-Requisite

To complete this workshop, you would require a developer account at Affinidi Portal and a few tools as listed below.

- An account on [Affinidi portal](https://portal.affinidi.com/)
- [Affinidi cli](https://docs.affinidi.com/dev-tools/affinidi-cli/)
- Implement [Affinidi Login](/Module-1/affinidi-login.md) to this application (Covered in Module 1)

You need to have the following installed on your machine:

- [NodeJs v18 and higher](https://nodejs.org). (it's recommended to use [nvm](https://github.com/nvm-sh/nvm))
- [VS Code](https://code.visualstudio.com/) or any familar IDE for development.

More details here in [Affinidi Documentation](https://docs.affinidi.com/docs/affinidi-elements/credential-issuance/)

> [!IMPORTANT]
> This sample App is an extension of the same App that we worked on for Affinidi Login.

&nbsp;

## Step-by-Step Guide to enable Affinidi Credential Issuance Service

Before proceeding with the steps below, make sure you have completed the prerequisites mentioned above.

> [!WARNING]
> The steps showcased in this sample application are provided only as a guide to quickly explore and learn how to integrate the components of Affinidi Trust Network into your application. This is NOT a Production-ready implementation. Do not deploy this to a production environment.
> &nbsp;

Now, let's continue with the step-by-step guide to enable the Affinidi Credentials Issuance service in the sample App.

### Install Dependencies

Install Dependencies for `Affinidi Credentials Issuance Client` And `Affinidi TDK Auth provider`

```javascript
npm install @affinidi-tdk/auth-provider @affinidi-tdk/credential-issuance-client
```

### Create required directories and files

```sh

mkdir -p src/pages/api/clients/
mkdir -p src/pages/api/credentials/

touch src/pages/credentials.tsx
touch src/pages/api/credentials/issuance-start.ts
touch src/pages/api/clients/credentials-client.ts
touch src/lib/variables.ts

```

### Create Personal Access Token (PAT)

Personal Access Token (PAT) is like a machine user that acts on your behalf to the Affinidi services. You can use the PAT to authenticate to the Affinidi services and automate specific tasks within your application. A Personal Access Token (PAT) lives outside of Projects, meaning PAT can access multiple projects once granted by the user.

More Details on [Personal Access Token](https://docs.affinidi.com/dev-tools/affinidi-cli/manage-token/#how-does-pat-authentication-works) here.

Personal Access Token is its needed for `Affinidi TDK Auth provider`.

You can refer the [Affinidi Documentation](https://docs.affinidi.com/dev-tools/affinidi-cli/manage-token/#affinidi-token-create-token) for creating pesronal access token from CLI.

**for eg**

1. Log in to Affinidi CLI by running

   ```sh
   affinidi start
   ```

2. Once logged in successfully, create a token by running the below command

   ```sh
   affinidi token create-token
   ```

   Follow the instruction

   ```
    ? Enter the value for name workshopPAT
    ? Generate a new keypair for the token? yes
    ? Enter a passphrase to encrypt the private key. Leave it empty for no encryption ******
    ? Add token to active project and grant permissions? yes
    ? Enter the allowed resources, separated by spaces. Use * to allow access to all project resources *
    ? Enter the allowed actions, separated by spaces. Use * to allow all actions *
   ```

   **Sample response**

   ```json
    Creating Personal Access Token... Created successfully!
    Adding token to active project... Added successfully!
    Granting permissions to token... Granted successfully!
    {
      "id": "**********",
      "ari": "ari:iam:::token/**********",
      "ownerAri": "ari:iam:::user/**********",
      "name": "workshopPAT",
      "scopes": [
        "openid",
        "offline_access"
      ],
      "authenticationMethod": {
        "type": "PRIVATE_KEY",
        "signingAlgorithm": "RS256",
        "publicKeyInfo": {
          "jwks": {
            "keys": [
              {
                "use": "sig",
                "kty": "RSA",
                "kid": "**********",
                "alg": "RS256",
                "n": "**********",
                "e": "AQAB"
              }
            ]
          }
        }
      }
    }

    Use the projectId, tokenId, privateKey, and passphrase (if provided) to use this token with Affinidi TDK
    {
      "tokenId": "*******",
      "projectId": "*******",
      "privateKey": "*******",
      "passphrase": "******"
    }
    ›   Warning:
    ›   Please save the privateKey and passphrase (if provided) somewhere safe. You will not be able to view them again.
    ›

   ```

   For more details on the command run the below command

   ```sh
   affinidi token create-token --help
   ```

### Configure Credential Issuance Configuration

To issue a Verifiable Credential, it is required to setup the **Issuance Configuration** on your project, where you select the **issuing wallet** and **supported schemas** to create a credential offer that the application issue

You can easily do this using the [Affinidi Portal](https://portal.affinidi.com)

1. Login on [Affinidi Portal](https://portal.affinidi.com)

2. Open the `Wallets` menu under the `Tools` section and click on `Create Wallet` with any name (e.g. `MyWallet`) and DID method as `did:key`.

For more information, refer to the [Wallets documentation](https://docs.affinidi.com/dev-tools/wallets)

3. Go to `Credential Issuance Service` under the `Services` section.

4. Click on `Create Configuration` and set the following fields:

   `Issuing Wallet`: Select Wallet Created the previous step
   `Lifetime of Credential Offer` as `600`

5. Add schemas by clicking on "Add new item" under `Supported Schemas`

**Schema 1** :

- _Schema_ as `Manual Input`,
- _Credential Type ID_ as `TworkshopSchemaV1R0`
- _JSON Schema URL_ as `https://schema.affinidi.io/TworkshopSchemaV1R0.json`
- _JSDON-LD Context URL_ = `https://schema.affinidi.io/TworkshopSchemaV1R0.jsonld`

&nbsp;

For more details on `Schema Builder` refer to [Affinidi documentation](https://docs.affinidi.com/docs/affinidi-elements/schema-builder/).

### Update environment variables

update `.env` file

```javascript
## frontend only variables

NEXT_PUBLIC_VAULT_URL="https://vault.affinidi.com/claim?credential_offer_uri"
NEXT_PUBLIC_CREDENTIAL_TYPE_ID="TworkshopSchemaV1R0"

## backend only variables, update details from personal access token response from above steps

TOKEN_ENDPOINT="https://apse1.auth.developer.affinidi.io/auth/oauth2/token"
API_GATEWAY_URL="https://apse1.api.affinidi.io"

PROJECT_ID=""
TOKEN_ID=""
PASSPHRASE=""
PRIVATE_KEY= ""

```

## Implement Application Code Changes

### Create frontend env reader

Create Next Auth frontend Variables `src/lib/variables.ts`

```javascript

export const vaultUrl = process.env.NEXT_PUBLIC_VAULT_URL!;
export const credentialTypeId = process.env.NEXT_PUBLIC_CREDENTIAL_TYPE_ID!;
```

### Update backend env reader

update env variables `src/lib/env.ts`

```javascript
export const apiGatewayUrl = process.env.API_GATEWAY_URL!;
export const projectId = process.env.PROJECT_ID!;
export const tokenEndpoint = process.env.TOKEN_ENDPOINT!;

export const privateKey = process.env.PRIVATE_KEY!;

export const passphrase = process.env.PASSPHRASE!;
export const tokenId = process.env.TOKEN_ID!;

```

### Create `/credentials` page with `form` and `button` with default values and session

Create `src/pages/credentials.tsx` Page

```javascript

import { FC, useEffect, useState } from "react";
import { getSession } from "next-auth/react";
import { Session } from "next-auth";
import { credentialTypeId, vaultUrl } from "@/lib/variables";

type CredentialsProps = {
  email?: string | null | undefined;
  name?: string;
  phoneNumber?: string;
  dob?: string;
  gender?: string;
  address?: string;
  postcode?: string;
  city?: string;
  country?: string;
};

const defaults: CredentialsProps = {
  email: "email",
  name: "name",
  phoneNumber: "phno",
  dob: "dob",
  gender: "gender",
  address: "address",
  postcode: "postcode",
  city: "city",
  country: "country",
};

const Credentials: FC = () => {
  const containerStyle: React.CSSProperties = {
    backgroundColor: "auto",
    height: "100vh",
    flexDirection: "column",
    justifyContent: "center",
  };

  const [session, setsession] = useState<Session>();
  const [credentials, setcredentials] = useState<CredentialsProps>(defaults);
  const [issuanceResponse, setissuanceResponse] = useState<any>();
  useEffect(() => {
    async function fetchsession() {
      const mySession = await getSession();
      if (!mySession) {
        console.log("No session found");
        return;
      }
      setsession(mySession);
      setcredentials({ ...credentials, email: mySession.user.email });
      console.log("Session found", mySession);
    }
    fetchsession();
  }, []);

  const handleSubmit = async (event: React.FormEvent) => {
    //your handleSubmit logic here
  };

  return (
    <div style={{ ...containerStyle, alignItems: "left" }}>
      <h1 className="text-2xl font-semibold pb-6">
        Affinidi Credentials Issuance
      </h1>
      <p>Holder DID: {session?.userId}</p>
      <br />
      {!issuanceResponse && (
        <form onSubmit={handleSubmit} className="space-y-4">
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Email
            </label>
            <input
              type="text"
              value={session?.user?.email ?? credentials.email ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, email: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
              required
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Name
            </label>
            <input
              type="text"
              value={credentials.name ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, name: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              phoneNumber
            </label>
            <input
              type="text"
              value={credentials.phoneNumber ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, phoneNumber: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Date Of Birth
            </label>
            <input
              type="text"
              value={credentials.dob ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, dob: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Gender
            </label>
            <input
              type="text"
              value={credentials.gender ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, gender: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Postal Code
            </label>
            <input
              type="text"
              value={credentials.postcode ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, postcode: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Address
            </label>
            <input
              type="text"
              value={credentials.address ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, address: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              City
            </label>
            <input
              type="text"
              value={credentials.city ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, city: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Country
            </label>
            <input
              type="text"
              value={credentials.country ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, country: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <button
            type="submit"
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 mr-5"
          >
            Submit
          </button>
          <button
            type="submit"
            onClick={() => window.open("/", "_self")}
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          >
            Home
          </button>
        </form>
      )}
      {issuanceResponse && (
        <div>
          <h2>Credentials Issued</h2>
          <br />
          <pre>{JSON.stringify(issuanceResponse, null, 2)}</pre>
          <br />
          <button
            type="submit"
            onClick={() =>
              window.open(
                `${vaultUrl}=${issuanceResponse?.credentialOfferUri}`,
                "_blank"
              )
            }
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 mr-5"
          >
            Save to Affinidi Vault
          </button>

          <button
            type="submit"
            onClick={() => setissuanceResponse(undefined)}
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 mr-5"
          >
            Reject Credentials
          </button>

          <button
            type="submit"
            onClick={() => window.open("/", "_self")}
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          >
            Return to Homepage
          </button>
        </div>
      )}
    </div>
  );
};

export default Credentials;

```

### Apply Action on `handleSubmit` function

Create `handleSubmit` function which invoke the Affinidi Credentials Issuance Service.

```javascript
const handleSubmit = async (event: React.FormEvent) => {
  event.preventDefault();
  console.log("Form submitted with credentials: ", credentials);

  const apiData = {
    credentialData: {
      email: credentials.email,
      name: credentials.name,
      phoneNumber: credentials.phoneNumber,
      dob: credentials.dob,
      gender: credentials.gender,
      address: credentials.address,
      postcode: credentials.postcode,
      city: credentials.city,
      country: credentials.country,
    },
    credentialTypeId: credentialTypeId,
    holderDid: session?.userId,
  };

  const response = await fetch("/api/credentials/issuance-start", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(apiData),
  });

  if (response.ok) {
    let DataResponse = await response.json();

    console.log("Credentials Issued");
    console.log(DataResponse);
    setissuanceResponse(DataResponse);
  } else {
    console.error("Failed to issue credentials");
  }
};
```

### Create frontend API used in `handleSubmit` function to pass details to backend API

Create Front-end API `src/pages/api/credentials/issuance-start.ts`

```javascript
import {
  StartIssuanceInput,
  StartIssuanceInputClaimModeEnum,
  StartIssuanceResponse,
} from "@affinidi-tdk/credential-issuance-client";
import { NextApiRequest, NextApiResponse } from "next";
import { CredentialsClient } from "../clients/credentials-client";

async function handler(
  req: NextApiRequest,
  res: NextApiResponse<StartIssuanceResponse>
) {
  const { holderDid, credentialTypeId, credentialData } = req.body;

  //   console.log("holderDid", holderDid);
  //   console.log("credentialTypeId", credentialTypeId);
  //   console.log("credentialData", credentialData);
  try {
    const apiData: StartIssuanceInput = {
      claimMode: StartIssuanceInputClaimModeEnum.TxCode,
      holderDid,
      data: [
        {
          credentialTypeId,
          credentialData: {
            ...credentialData,
            // Add any additional data here
          },
        },
      ],
    };
    //console.log("apiData", apiData);

    const issuanceResult = await CredentialsClient.IssuanceStart(apiData);

    // console.log("issuanceResult post backend call", issuanceResult);

    res.status(200).json(issuanceResult);
  } catch (error: any) {
    {
      response: error.response?.data ?? error;
    }
    ("Issuance failed");

    throw error;
  }
}

export default handler;

```

### Create backend API to call `Affinidi TDK` for Affinidi Auth Provider and Affinidi Credentials Issuance Client

create backend API Call `src/pages/api/clients/credentials-client.ts`

```javascript
import {
  apiGatewayUrl,
  passphrase,
  privateKey,
  projectId,
  tokenEndpoint,
  tokenId,
} from "@/lib/env";
import { AuthProvider } from "@affinidi-tdk/auth-provider";
import {
  IssuanceApi,
  Configuration as IssuanceConfiguration,
  StartIssuanceInput,
} from "@affinidi-tdk/credential-issuance-client";

const stats = {
  apiGatewayUrl: apiGatewayUrl,
  tokenEndpoint: tokenEndpoint,

  tokenId: tokenId,
  passphrase: passphrase,
  privateKey: privateKey,

  projectId: projectId,
};
const authProvider = new AuthProvider(stats);

export const CredentialsClient = {
  IssuanceStart: async (apiData: StartIssuanceInput) => {
    const api = new IssuanceApi(
      new IssuanceConfiguration({
        apiKey: authProvider.fetchProjectScopedToken.bind(authProvider),
        basePath: `${apiGatewayUrl}/cis`,
      })
    );

    const { data } = await api.startIssuance(projectId, apiData);
    console.log("startIssuance response", data);
    return data;
  },
};
```

### Run The application to experience Affinidi Credentials Issuance

Try the App with Affinidi Login & Affinidi CIS

```sh
npm run dev
```

Open [http://localhost:3000/credentials](http://localhost:3000/credentials) with your browser to see the result.

## Next Module

- [**Module 3: Building Consent-Driven Data Access**](/Module-3/iota-framework.MD)

## Move to

- [**Module 1: Implementing Passwordless Authentication**](/Module-1/affinidi-login.md)
- [**Homepage**](/README.md)

## More Resources for Advanced Learning

- [Affinidi Documentation](https://docs.affinidi.com/docs/affinidi-elements/credential-issuance/)
- [Affinidi Reference Apps](https://docs.affinidi.com/docs/affinidi-elements/credential-issuance/sample-app-nextjs/)
