This file is a merged representation of the entire codebase, combined into a single document by Repomix.

# File Summary

## Purpose
This file contains a packed representation of the entire repository's contents.
It is designed to be easily consumable by AI systems for analysis, code review,
or other automated processes.

## File Format
The content is organized as follows:
1. This summary section
2. Repository information
3. Directory structure
4. Multiple file entries, each consisting of:
  a. A header with the file path (## File: path/to/file)
  b. The full contents of the file in a code block

## Usage Guidelines
- This file should be treated as read-only. Any changes should be made to the
  original repository files, not this packed version.
- When processing this file, use the file path to distinguish
  between different files in the repository.
- Be aware that this file may contain sensitive information. Handle it with
  the same level of security as you would the original repository.

## Notes
- Some files may have been excluded based on .gitignore rules and Repomix's configuration
- Binary files are not included in this packed representation. Please refer to the Repository Structure section for a complete list of file paths, including binary files
- Files matching patterns in .gitignore are excluded
- Files matching default ignore patterns are excluded

## Additional Info

# Directory Structure
```
src/
  app/
    (frontend)/
      layout.tsx
      page.tsx
      styles.css
    (payload)/
      admin/
        [[...segments]]/
          not-found.tsx
          page.tsx
        importMap.js
      api/
        [...slug]/
          route.ts
        graphql/
          route.ts
        graphql-playground/
          route.ts
      layout.tsx
    my-route/
      route.ts
  collections/
    Media.ts
    Users.ts
  migrations/
    20250303_013923_initial.json
    20250303_013923_initial.ts
    index.ts
  payload-types.ts
  payload.config.ts
.env.example
.gitignore
.prettierrc.json
.yarnrc
docker-compose.yml
Dockerfile
eslint.config.mjs
next.config.mjs
package.json
README.md
tsconfig.json
```

# Files

## File: src/app/(frontend)/layout.tsx
```typescript
import React from 'react'
import './styles.css'

export const metadata = {
  description: 'A blank template using Payload in a Next.js app.',
  title: 'Payload Blank Template',
}

export default async function RootLayout(props: { children: React.ReactNode }) {
  const { children } = props

  return (
    <html lang="en">
      <body>
        <main>{children}</main>
      </body>
    </html>
  )
}
```

## File: src/app/(frontend)/page.tsx
```typescript
import { headers as getHeaders } from 'next/headers.js'
import Image from 'next/image'
import { getPayload } from 'payload'
import React from 'react'
import { fileURLToPath } from 'url'

import config from '@/payload.config'
import './styles.css'

export default async function HomePage() {
  const headers = await getHeaders()
  const payloadConfig = await config
  const payload = await getPayload({ config: payloadConfig })
  const { user } = await payload.auth({ headers })

  const fileURL = `vscode://file/${fileURLToPath(import.meta.url)}`

  return (
    <div className="home">
      <div className="content">
        <picture>
          <source srcSet="https://raw.githubusercontent.com/payloadcms/payload/main/packages/ui/src/assets/payload-favicon.svg" />
          <Image
            alt="Payload Logo"
            height={65}
            src="https://raw.githubusercontent.com/payloadcms/payload/main/packages/ui/src/assets/payload-favicon.svg"
            width={65}
          />
        </picture>
        {!user && <h1>Welcome to your new project.</h1>}
        {user && <h1>Welcome back, {user.email}</h1>}
        <div className="links">
          <a
            className="admin"
            href={payloadConfig.routes.admin}
            rel="noopener noreferrer"
            target="_blank"
          >
            Go to admin panel
          </a>
          <a
            className="docs"
            href="https://payloadcms.com/docs"
            rel="noopener noreferrer"
            target="_blank"
          >
            Documentation
          </a>
        </div>
      </div>
      <div className="footer">
        <p>Update this page by editing</p>
        <a className="codeLink" href={fileURL}>
          <code>app/(frontend)/page.tsx</code>
        </a>
      </div>
    </div>
  )
}
```

## File: src/app/(frontend)/styles.css
```css
:root {
  --font-mono: 'Roboto Mono', monospace;
}

* {
  box-sizing: border-box;
}

html {
  font-size: 18px;
  line-height: 32px;

  background: rgb(0, 0, 0);
  -webkit-font-smoothing: antialiased;
}

html,
body,
#app {
  height: 100%;
}

body {
  font-family: system-ui;
  font-size: 18px;
  line-height: 32px;

  margin: 0;
  color: rgb(1000, 1000, 1000);

  @media (max-width: 1024px) {
    font-size: 15px;
    line-height: 24px;
  }
}

img {
  max-width: 100%;
  height: auto;
  display: block;
}

h1 {
  margin: 40px 0;
  font-size: 64px;
  line-height: 70px;
  font-weight: bold;

  @media (max-width: 1024px) {
    margin: 24px 0;
    font-size: 42px;
    line-height: 42px;
  }

  @media (max-width: 768px) {
    font-size: 38px;
    line-height: 38px;
  }

  @media (max-width: 400px) {
    font-size: 32px;
    line-height: 32px;
  }
}

p {
  margin: 24px 0;

  @media (max-width: 1024px) {
    margin: calc(var(--base) * 0.75) 0;
  }
}

a {
  color: currentColor;

  &:focus {
    opacity: 0.8;
    outline: none;
  }

  &:active {
    opacity: 0.7;
    outline: none;
  }
}

svg {
  vertical-align: middle;
}

.home {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  height: 100vh;
  padding: 45px;
  max-width: 1024px;
  margin: 0 auto;
  overflow: hidden;

  @media (max-width: 400px) {
    padding: 24px;
  }

  .content {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    flex-grow: 1;

    h1 {
      text-align: center;
    }
  }

  .links {
    display: flex;
    align-items: center;
    gap: 12px;

    a {
      text-decoration: none;
      padding: 0.25rem 0.5rem;
      border-radius: 4px;
    }

    .admin {
      color: rgb(0, 0, 0);
      background: rgb(1000, 1000, 1000);
      border: 1px solid rgb(0, 0, 0);
    }

    .docs {
      color: rgb(1000, 1000, 1000);
      background: rgb(0, 0, 0);
      border: 1px solid rgb(1000, 1000, 1000);
    }
  }

  .footer {
    display: flex;
    align-items: center;
    gap: 8px;

    @media (max-width: 1024px) {
      flex-direction: column;
      gap: 6px;
    }

    p {
      margin: 0;
    }

    .codeLink {
      text-decoration: none;
      padding: 0 0.5rem;
      background: rgb(60, 60, 60);
      border-radius: 4px;
    }
  }
}
```

## File: src/app/(payload)/admin/[[...segments]]/not-found.tsx
```typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import type { Metadata } from 'next'

import config from '@payload-config'
import { NotFoundPage, generatePageMetadata } from '@payloadcms/next/views'
import { importMap } from '../importMap'

type Args = {
  params: Promise<{
    segments: string[]
  }>
  searchParams: Promise<{
    [key: string]: string | string[]
  }>
}

export const generateMetadata = ({ params, searchParams }: Args): Promise<Metadata> =>
  generatePageMetadata({ config, params, searchParams })

const NotFound = ({ params, searchParams }: Args) =>
  NotFoundPage({ config, params, searchParams, importMap })

export default NotFound
```

## File: src/app/(payload)/admin/[[...segments]]/page.tsx
```typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import type { Metadata } from 'next'

import config from '@payload-config'
import { RootPage, generatePageMetadata } from '@payloadcms/next/views'
import { importMap } from '../importMap'

type Args = {
  params: Promise<{
    segments: string[]
  }>
  searchParams: Promise<{
    [key: string]: string | string[]
  }>
}

export const generateMetadata = ({ params, searchParams }: Args): Promise<Metadata> =>
  generatePageMetadata({ config, params, searchParams })

const Page = ({ params, searchParams }: Args) =>
  RootPage({ config, params, searchParams, importMap })

export default Page
```

## File: src/app/(payload)/admin/importMap.js
```javascript
export const importMap = {}
```

## File: src/app/(payload)/api/[...slug]/route.ts
```typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import config from '@payload-config'
import '@payloadcms/next/css'
import {
  REST_DELETE,
  REST_GET,
  REST_OPTIONS,
  REST_PATCH,
  REST_POST,
  REST_PUT,
} from '@payloadcms/next/routes'

export const GET = REST_GET(config)
export const POST = REST_POST(config)
export const DELETE = REST_DELETE(config)
export const PATCH = REST_PATCH(config)
export const PUT = REST_PUT(config)
export const OPTIONS = REST_OPTIONS(config)
```

## File: src/app/(payload)/api/graphql/route.ts
```typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import config from '@payload-config'
import { GRAPHQL_POST, REST_OPTIONS } from '@payloadcms/next/routes'

export const POST = GRAPHQL_POST(config)

export const OPTIONS = REST_OPTIONS(config)
```

## File: src/app/(payload)/api/graphql-playground/route.ts
```typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import config from '@payload-config'
import '@payloadcms/next/css'
import { GRAPHQL_PLAYGROUND_GET } from '@payloadcms/next/routes'

export const GET = GRAPHQL_PLAYGROUND_GET(config)
```

## File: src/app/(payload)/layout.tsx
```typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import config from '@payload-config'
import '@payloadcms/next/css'
import type { ServerFunctionClient } from 'payload'
import { handleServerFunctions, RootLayout } from '@payloadcms/next/layouts'
import React from 'react'

import { importMap } from './admin/importMap.js'
import './custom.scss'

type Args = {
  children: React.ReactNode
}

const serverFunction: ServerFunctionClient = async function (args) {
  'use server'
  return handleServerFunctions({
    ...args,
    config,
    importMap,
  })
}

const Layout = ({ children }: Args) => (
  <RootLayout config={config} importMap={importMap} serverFunction={serverFunction}>
    {children}
  </RootLayout>
)

export default Layout
```

## File: src/app/my-route/route.ts
```typescript
import configPromise from '@payload-config'
import { getPayload } from 'payload'

export const GET = async () => {
  const payload = await getPayload({
    config: configPromise,
  })

  const data = await payload.find({
    collection: 'users',
  })

  return Response.json(data)
}
```

## File: src/collections/Media.ts
```typescript
import type { CollectionConfig } from 'payload'

export const Media: CollectionConfig = {
  slug: 'media',
  access: {
    read: () => true,
  },
  fields: [
    {
      name: 'alt',
      type: 'text',
      required: true,
    },
  ],
  upload: true,
}
```

## File: src/collections/Users.ts
```typescript
import type { CollectionConfig } from 'payload'

export const Users: CollectionConfig = {
  slug: 'users',
  admin: {
    useAsTitle: 'email',
  },
  auth: true,
  fields: [
    // Email added by default
    // Add more fields as needed
  ],
}
```

## File: src/migrations/20250303_013923_initial.json
```json
{
  "id": "4d0dd218-d96c-4cf1-9ccf-0fbc54bb2798",
  "prevId": "00000000-0000-0000-0000-000000000000",
  "version": "7",
  "dialect": "postgresql",
  "tables": {
    "public.users": {
      "name": "users",
      "schema": "",
      "columns": {
        "id": {
          "name": "id",
          "type": "serial",
          "primaryKey": true,
          "notNull": true
        },
        "updated_at": {
          "name": "updated_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        },
        "created_at": {
          "name": "created_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        },
        "email": {
          "name": "email",
          "type": "varchar",
          "primaryKey": false,
          "notNull": true
        },
        "reset_password_token": {
          "name": "reset_password_token",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "reset_password_expiration": {
          "name": "reset_password_expiration",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": false
        },
        "salt": {
          "name": "salt",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "hash": {
          "name": "hash",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "login_attempts": {
          "name": "login_attempts",
          "type": "numeric",
          "primaryKey": false,
          "notNull": false,
          "default": 0
        },
        "lock_until": {
          "name": "lock_until",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": false
        }
      },
      "indexes": {
        "users_updated_at_idx": {
          "name": "users_updated_at_idx",
          "columns": [
            {
              "expression": "updated_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "users_created_at_idx": {
          "name": "users_created_at_idx",
          "columns": [
            {
              "expression": "created_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "users_email_idx": {
          "name": "users_email_idx",
          "columns": [
            {
              "expression": "email",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": true,
          "concurrently": false,
          "method": "btree",
          "with": {}
        }
      },
      "foreignKeys": {},
      "compositePrimaryKeys": {},
      "uniqueConstraints": {},
      "policies": {},
      "checkConstraints": {},
      "isRLSEnabled": false
    },
    "public.media": {
      "name": "media",
      "schema": "",
      "columns": {
        "id": {
          "name": "id",
          "type": "serial",
          "primaryKey": true,
          "notNull": true
        },
        "alt": {
          "name": "alt",
          "type": "varchar",
          "primaryKey": false,
          "notNull": true
        },
        "updated_at": {
          "name": "updated_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        },
        "created_at": {
          "name": "created_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        },
        "url": {
          "name": "url",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "thumbnail_u_r_l": {
          "name": "thumbnail_u_r_l",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "filename": {
          "name": "filename",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "mime_type": {
          "name": "mime_type",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "filesize": {
          "name": "filesize",
          "type": "numeric",
          "primaryKey": false,
          "notNull": false
        },
        "width": {
          "name": "width",
          "type": "numeric",
          "primaryKey": false,
          "notNull": false
        },
        "height": {
          "name": "height",
          "type": "numeric",
          "primaryKey": false,
          "notNull": false
        },
        "focal_x": {
          "name": "focal_x",
          "type": "numeric",
          "primaryKey": false,
          "notNull": false
        },
        "focal_y": {
          "name": "focal_y",
          "type": "numeric",
          "primaryKey": false,
          "notNull": false
        }
      },
      "indexes": {
        "media_updated_at_idx": {
          "name": "media_updated_at_idx",
          "columns": [
            {
              "expression": "updated_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "media_created_at_idx": {
          "name": "media_created_at_idx",
          "columns": [
            {
              "expression": "created_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "media_filename_idx": {
          "name": "media_filename_idx",
          "columns": [
            {
              "expression": "filename",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": true,
          "concurrently": false,
          "method": "btree",
          "with": {}
        }
      },
      "foreignKeys": {},
      "compositePrimaryKeys": {},
      "uniqueConstraints": {},
      "policies": {},
      "checkConstraints": {},
      "isRLSEnabled": false
    },
    "public.payload_locked_documents": {
      "name": "payload_locked_documents",
      "schema": "",
      "columns": {
        "id": {
          "name": "id",
          "type": "serial",
          "primaryKey": true,
          "notNull": true
        },
        "global_slug": {
          "name": "global_slug",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "updated_at": {
          "name": "updated_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        },
        "created_at": {
          "name": "created_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        }
      },
      "indexes": {
        "payload_locked_documents_global_slug_idx": {
          "name": "payload_locked_documents_global_slug_idx",
          "columns": [
            {
              "expression": "global_slug",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_locked_documents_updated_at_idx": {
          "name": "payload_locked_documents_updated_at_idx",
          "columns": [
            {
              "expression": "updated_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_locked_documents_created_at_idx": {
          "name": "payload_locked_documents_created_at_idx",
          "columns": [
            {
              "expression": "created_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        }
      },
      "foreignKeys": {},
      "compositePrimaryKeys": {},
      "uniqueConstraints": {},
      "policies": {},
      "checkConstraints": {},
      "isRLSEnabled": false
    },
    "public.payload_locked_documents_rels": {
      "name": "payload_locked_documents_rels",
      "schema": "",
      "columns": {
        "id": {
          "name": "id",
          "type": "serial",
          "primaryKey": true,
          "notNull": true
        },
        "order": {
          "name": "order",
          "type": "integer",
          "primaryKey": false,
          "notNull": false
        },
        "parent_id": {
          "name": "parent_id",
          "type": "integer",
          "primaryKey": false,
          "notNull": true
        },
        "path": {
          "name": "path",
          "type": "varchar",
          "primaryKey": false,
          "notNull": true
        },
        "users_id": {
          "name": "users_id",
          "type": "integer",
          "primaryKey": false,
          "notNull": false
        },
        "media_id": {
          "name": "media_id",
          "type": "integer",
          "primaryKey": false,
          "notNull": false
        }
      },
      "indexes": {
        "payload_locked_documents_rels_order_idx": {
          "name": "payload_locked_documents_rels_order_idx",
          "columns": [
            {
              "expression": "order",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_locked_documents_rels_parent_idx": {
          "name": "payload_locked_documents_rels_parent_idx",
          "columns": [
            {
              "expression": "parent_id",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_locked_documents_rels_path_idx": {
          "name": "payload_locked_documents_rels_path_idx",
          "columns": [
            {
              "expression": "path",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_locked_documents_rels_users_id_idx": {
          "name": "payload_locked_documents_rels_users_id_idx",
          "columns": [
            {
              "expression": "users_id",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_locked_documents_rels_media_id_idx": {
          "name": "payload_locked_documents_rels_media_id_idx",
          "columns": [
            {
              "expression": "media_id",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        }
      },
      "foreignKeys": {
        "payload_locked_documents_rels_parent_fk": {
          "name": "payload_locked_documents_rels_parent_fk",
          "tableFrom": "payload_locked_documents_rels",
          "tableTo": "payload_locked_documents",
          "columnsFrom": ["parent_id"],
          "columnsTo": ["id"],
          "onDelete": "cascade",
          "onUpdate": "no action"
        },
        "payload_locked_documents_rels_users_fk": {
          "name": "payload_locked_documents_rels_users_fk",
          "tableFrom": "payload_locked_documents_rels",
          "tableTo": "users",
          "columnsFrom": ["users_id"],
          "columnsTo": ["id"],
          "onDelete": "cascade",
          "onUpdate": "no action"
        },
        "payload_locked_documents_rels_media_fk": {
          "name": "payload_locked_documents_rels_media_fk",
          "tableFrom": "payload_locked_documents_rels",
          "tableTo": "media",
          "columnsFrom": ["media_id"],
          "columnsTo": ["id"],
          "onDelete": "cascade",
          "onUpdate": "no action"
        }
      },
      "compositePrimaryKeys": {},
      "uniqueConstraints": {},
      "policies": {},
      "checkConstraints": {},
      "isRLSEnabled": false
    },
    "public.payload_preferences": {
      "name": "payload_preferences",
      "schema": "",
      "columns": {
        "id": {
          "name": "id",
          "type": "serial",
          "primaryKey": true,
          "notNull": true
        },
        "key": {
          "name": "key",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "value": {
          "name": "value",
          "type": "jsonb",
          "primaryKey": false,
          "notNull": false
        },
        "updated_at": {
          "name": "updated_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        },
        "created_at": {
          "name": "created_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        }
      },
      "indexes": {
        "payload_preferences_key_idx": {
          "name": "payload_preferences_key_idx",
          "columns": [
            {
              "expression": "key",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_preferences_updated_at_idx": {
          "name": "payload_preferences_updated_at_idx",
          "columns": [
            {
              "expression": "updated_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_preferences_created_at_idx": {
          "name": "payload_preferences_created_at_idx",
          "columns": [
            {
              "expression": "created_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        }
      },
      "foreignKeys": {},
      "compositePrimaryKeys": {},
      "uniqueConstraints": {},
      "policies": {},
      "checkConstraints": {},
      "isRLSEnabled": false
    },
    "public.payload_preferences_rels": {
      "name": "payload_preferences_rels",
      "schema": "",
      "columns": {
        "id": {
          "name": "id",
          "type": "serial",
          "primaryKey": true,
          "notNull": true
        },
        "order": {
          "name": "order",
          "type": "integer",
          "primaryKey": false,
          "notNull": false
        },
        "parent_id": {
          "name": "parent_id",
          "type": "integer",
          "primaryKey": false,
          "notNull": true
        },
        "path": {
          "name": "path",
          "type": "varchar",
          "primaryKey": false,
          "notNull": true
        },
        "users_id": {
          "name": "users_id",
          "type": "integer",
          "primaryKey": false,
          "notNull": false
        }
      },
      "indexes": {
        "payload_preferences_rels_order_idx": {
          "name": "payload_preferences_rels_order_idx",
          "columns": [
            {
              "expression": "order",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_preferences_rels_parent_idx": {
          "name": "payload_preferences_rels_parent_idx",
          "columns": [
            {
              "expression": "parent_id",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_preferences_rels_path_idx": {
          "name": "payload_preferences_rels_path_idx",
          "columns": [
            {
              "expression": "path",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_preferences_rels_users_id_idx": {
          "name": "payload_preferences_rels_users_id_idx",
          "columns": [
            {
              "expression": "users_id",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        }
      },
      "foreignKeys": {
        "payload_preferences_rels_parent_fk": {
          "name": "payload_preferences_rels_parent_fk",
          "tableFrom": "payload_preferences_rels",
          "tableTo": "payload_preferences",
          "columnsFrom": ["parent_id"],
          "columnsTo": ["id"],
          "onDelete": "cascade",
          "onUpdate": "no action"
        },
        "payload_preferences_rels_users_fk": {
          "name": "payload_preferences_rels_users_fk",
          "tableFrom": "payload_preferences_rels",
          "tableTo": "users",
          "columnsFrom": ["users_id"],
          "columnsTo": ["id"],
          "onDelete": "cascade",
          "onUpdate": "no action"
        }
      },
      "compositePrimaryKeys": {},
      "uniqueConstraints": {},
      "policies": {},
      "checkConstraints": {},
      "isRLSEnabled": false
    },
    "public.payload_migrations": {
      "name": "payload_migrations",
      "schema": "",
      "columns": {
        "id": {
          "name": "id",
          "type": "serial",
          "primaryKey": true,
          "notNull": true
        },
        "name": {
          "name": "name",
          "type": "varchar",
          "primaryKey": false,
          "notNull": false
        },
        "batch": {
          "name": "batch",
          "type": "numeric",
          "primaryKey": false,
          "notNull": false
        },
        "updated_at": {
          "name": "updated_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        },
        "created_at": {
          "name": "created_at",
          "type": "timestamp(3) with time zone",
          "primaryKey": false,
          "notNull": true,
          "default": "now()"
        }
      },
      "indexes": {
        "payload_migrations_updated_at_idx": {
          "name": "payload_migrations_updated_at_idx",
          "columns": [
            {
              "expression": "updated_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        },
        "payload_migrations_created_at_idx": {
          "name": "payload_migrations_created_at_idx",
          "columns": [
            {
              "expression": "created_at",
              "isExpression": false,
              "asc": true,
              "nulls": "last"
            }
          ],
          "isUnique": false,
          "concurrently": false,
          "method": "btree",
          "with": {}
        }
      },
      "foreignKeys": {},
      "compositePrimaryKeys": {},
      "uniqueConstraints": {},
      "policies": {},
      "checkConstraints": {},
      "isRLSEnabled": false
    }
  },
  "enums": {},
  "schemas": {},
  "sequences": {},
  "roles": {},
  "policies": {},
  "views": {},
  "_meta": {
    "schemas": {},
    "tables": {},
    "columns": {}
  }
}
```

## File: src/migrations/20250303_013923_initial.ts
```typescript
import { MigrateUpArgs, MigrateDownArgs, sql } from '@payloadcms/db-postgres'

export async function up({ db, payload, req }: MigrateUpArgs): Promise<void> {
  await db.execute(sql`
   CREATE TABLE IF NOT EXISTS "users" (
  	"id" serial PRIMARY KEY NOT NULL,
  	"updated_at" timestamp(3) with time zone DEFAULT now() NOT NULL,
  	"created_at" timestamp(3) with time zone DEFAULT now() NOT NULL,
  	"email" varchar NOT NULL,
  	"reset_password_token" varchar,
  	"reset_password_expiration" timestamp(3) with time zone,
  	"salt" varchar,
  	"hash" varchar,
  	"login_attempts" numeric DEFAULT 0,
  	"lock_until" timestamp(3) with time zone
  );
  
  CREATE TABLE IF NOT EXISTS "media" (
  	"id" serial PRIMARY KEY NOT NULL,
  	"alt" varchar NOT NULL,
  	"updated_at" timestamp(3) with time zone DEFAULT now() NOT NULL,
  	"created_at" timestamp(3) with time zone DEFAULT now() NOT NULL,
  	"url" varchar,
  	"thumbnail_u_r_l" varchar,
  	"filename" varchar,
  	"mime_type" varchar,
  	"filesize" numeric,
  	"width" numeric,
  	"height" numeric,
  	"focal_x" numeric,
  	"focal_y" numeric
  );
  
  CREATE TABLE IF NOT EXISTS "payload_locked_documents" (
  	"id" serial PRIMARY KEY NOT NULL,
  	"global_slug" varchar,
  	"updated_at" timestamp(3) with time zone DEFAULT now() NOT NULL,
  	"created_at" timestamp(3) with time zone DEFAULT now() NOT NULL
  );
  
  CREATE TABLE IF NOT EXISTS "payload_locked_documents_rels" (
  	"id" serial PRIMARY KEY NOT NULL,
  	"order" integer,
  	"parent_id" integer NOT NULL,
  	"path" varchar NOT NULL,
  	"users_id" integer,
  	"media_id" integer
  );
  
  CREATE TABLE IF NOT EXISTS "payload_preferences" (
  	"id" serial PRIMARY KEY NOT NULL,
  	"key" varchar,
  	"value" jsonb,
  	"updated_at" timestamp(3) with time zone DEFAULT now() NOT NULL,
  	"created_at" timestamp(3) with time zone DEFAULT now() NOT NULL
  );
  
  CREATE TABLE IF NOT EXISTS "payload_preferences_rels" (
  	"id" serial PRIMARY KEY NOT NULL,
  	"order" integer,
  	"parent_id" integer NOT NULL,
  	"path" varchar NOT NULL,
  	"users_id" integer
  );
  
  CREATE TABLE IF NOT EXISTS "payload_migrations" (
  	"id" serial PRIMARY KEY NOT NULL,
  	"name" varchar,
  	"batch" numeric,
  	"updated_at" timestamp(3) with time zone DEFAULT now() NOT NULL,
  	"created_at" timestamp(3) with time zone DEFAULT now() NOT NULL
  );
  
  DO $$ BEGIN
   ALTER TABLE "payload_locked_documents_rels" ADD CONSTRAINT "payload_locked_documents_rels_parent_fk" FOREIGN KEY ("parent_id") REFERENCES "public"."payload_locked_documents"("id") ON DELETE cascade ON UPDATE no action;
  EXCEPTION
   WHEN duplicate_object THEN null;
  END $$;
  
  DO $$ BEGIN
   ALTER TABLE "payload_locked_documents_rels" ADD CONSTRAINT "payload_locked_documents_rels_users_fk" FOREIGN KEY ("users_id") REFERENCES "public"."users"("id") ON DELETE cascade ON UPDATE no action;
  EXCEPTION
   WHEN duplicate_object THEN null;
  END $$;
  
  DO $$ BEGIN
   ALTER TABLE "payload_locked_documents_rels" ADD CONSTRAINT "payload_locked_documents_rels_media_fk" FOREIGN KEY ("media_id") REFERENCES "public"."media"("id") ON DELETE cascade ON UPDATE no action;
  EXCEPTION
   WHEN duplicate_object THEN null;
  END $$;
  
  DO $$ BEGIN
   ALTER TABLE "payload_preferences_rels" ADD CONSTRAINT "payload_preferences_rels_parent_fk" FOREIGN KEY ("parent_id") REFERENCES "public"."payload_preferences"("id") ON DELETE cascade ON UPDATE no action;
  EXCEPTION
   WHEN duplicate_object THEN null;
  END $$;
  
  DO $$ BEGIN
   ALTER TABLE "payload_preferences_rels" ADD CONSTRAINT "payload_preferences_rels_users_fk" FOREIGN KEY ("users_id") REFERENCES "public"."users"("id") ON DELETE cascade ON UPDATE no action;
  EXCEPTION
   WHEN duplicate_object THEN null;
  END $$;
  
  CREATE INDEX IF NOT EXISTS "users_updated_at_idx" ON "users" USING btree ("updated_at");
  CREATE INDEX IF NOT EXISTS "users_created_at_idx" ON "users" USING btree ("created_at");
  CREATE UNIQUE INDEX IF NOT EXISTS "users_email_idx" ON "users" USING btree ("email");
  CREATE INDEX IF NOT EXISTS "media_updated_at_idx" ON "media" USING btree ("updated_at");
  CREATE INDEX IF NOT EXISTS "media_created_at_idx" ON "media" USING btree ("created_at");
  CREATE UNIQUE INDEX IF NOT EXISTS "media_filename_idx" ON "media" USING btree ("filename");
  CREATE INDEX IF NOT EXISTS "payload_locked_documents_global_slug_idx" ON "payload_locked_documents" USING btree ("global_slug");
  CREATE INDEX IF NOT EXISTS "payload_locked_documents_updated_at_idx" ON "payload_locked_documents" USING btree ("updated_at");
  CREATE INDEX IF NOT EXISTS "payload_locked_documents_created_at_idx" ON "payload_locked_documents" USING btree ("created_at");
  CREATE INDEX IF NOT EXISTS "payload_locked_documents_rels_order_idx" ON "payload_locked_documents_rels" USING btree ("order");
  CREATE INDEX IF NOT EXISTS "payload_locked_documents_rels_parent_idx" ON "payload_locked_documents_rels" USING btree ("parent_id");
  CREATE INDEX IF NOT EXISTS "payload_locked_documents_rels_path_idx" ON "payload_locked_documents_rels" USING btree ("path");
  CREATE INDEX IF NOT EXISTS "payload_locked_documents_rels_users_id_idx" ON "payload_locked_documents_rels" USING btree ("users_id");
  CREATE INDEX IF NOT EXISTS "payload_locked_documents_rels_media_id_idx" ON "payload_locked_documents_rels" USING btree ("media_id");
  CREATE INDEX IF NOT EXISTS "payload_preferences_key_idx" ON "payload_preferences" USING btree ("key");
  CREATE INDEX IF NOT EXISTS "payload_preferences_updated_at_idx" ON "payload_preferences" USING btree ("updated_at");
  CREATE INDEX IF NOT EXISTS "payload_preferences_created_at_idx" ON "payload_preferences" USING btree ("created_at");
  CREATE INDEX IF NOT EXISTS "payload_preferences_rels_order_idx" ON "payload_preferences_rels" USING btree ("order");
  CREATE INDEX IF NOT EXISTS "payload_preferences_rels_parent_idx" ON "payload_preferences_rels" USING btree ("parent_id");
  CREATE INDEX IF NOT EXISTS "payload_preferences_rels_path_idx" ON "payload_preferences_rels" USING btree ("path");
  CREATE INDEX IF NOT EXISTS "payload_preferences_rels_users_id_idx" ON "payload_preferences_rels" USING btree ("users_id");
  CREATE INDEX IF NOT EXISTS "payload_migrations_updated_at_idx" ON "payload_migrations" USING btree ("updated_at");
  CREATE INDEX IF NOT EXISTS "payload_migrations_created_at_idx" ON "payload_migrations" USING btree ("created_at");`)
}

export async function down({ db, payload, req }: MigrateDownArgs): Promise<void> {
  await db.execute(sql`
   DROP TABLE "users" CASCADE;
  DROP TABLE "media" CASCADE;
  DROP TABLE "payload_locked_documents" CASCADE;
  DROP TABLE "payload_locked_documents_rels" CASCADE;
  DROP TABLE "payload_preferences" CASCADE;
  DROP TABLE "payload_preferences_rels" CASCADE;
  DROP TABLE "payload_migrations" CASCADE;`)
}
```

## File: src/migrations/index.ts
```typescript
import * as migration_20250303_013923_initial from './20250303_013923_initial'

export const migrations = [
  {
    up: migration_20250303_013923_initial.up,
    down: migration_20250303_013923_initial.down,
    name: '20250303_013923_initial',
  },
]
```

## File: src/payload-types.ts
```typescript
/* tslint:disable */
/* eslint-disable */
/**
 * This file was automatically generated by Payload.
 * DO NOT MODIFY IT BY HAND. Instead, modify your source Payload config,
 * and re-run `payload generate:types` to regenerate this file.
 */

/**
 * Supported timezones in IANA format.
 *
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "supportedTimezones".
 */
export type SupportedTimezones =
  | 'Pacific/Midway'
  | 'Pacific/Niue'
  | 'Pacific/Honolulu'
  | 'Pacific/Rarotonga'
  | 'America/Anchorage'
  | 'Pacific/Gambier'
  | 'America/Los_Angeles'
  | 'America/Tijuana'
  | 'America/Denver'
  | 'America/Phoenix'
  | 'America/Chicago'
  | 'America/Guatemala'
  | 'America/New_York'
  | 'America/Bogota'
  | 'America/Caracas'
  | 'America/Santiago'
  | 'America/Buenos_Aires'
  | 'America/Sao_Paulo'
  | 'Atlantic/South_Georgia'
  | 'Atlantic/Azores'
  | 'Atlantic/Cape_Verde'
  | 'Europe/London'
  | 'Europe/Berlin'
  | 'Africa/Lagos'
  | 'Europe/Athens'
  | 'Africa/Cairo'
  | 'Europe/Moscow'
  | 'Asia/Riyadh'
  | 'Asia/Dubai'
  | 'Asia/Baku'
  | 'Asia/Karachi'
  | 'Asia/Tashkent'
  | 'Asia/Calcutta'
  | 'Asia/Dhaka'
  | 'Asia/Almaty'
  | 'Asia/Jakarta'
  | 'Asia/Bangkok'
  | 'Asia/Shanghai'
  | 'Asia/Singapore'
  | 'Asia/Tokyo'
  | 'Asia/Seoul'
  | 'Australia/Sydney'
  | 'Pacific/Guam'
  | 'Pacific/Noumea'
  | 'Pacific/Auckland'
  | 'Pacific/Fiji';

export interface Config {
  auth: {
    users: UserAuthOperations;
  };
  blocks: {};
  collections: {
    users: User;
    media: Media;
    'payload-locked-documents': PayloadLockedDocument;
    'payload-preferences': PayloadPreference;
    'payload-migrations': PayloadMigration;
  };
  collectionsJoins: {};
  collectionsSelect: {
    users: UsersSelect<false> | UsersSelect<true>;
    media: MediaSelect<false> | MediaSelect<true>;
    'payload-locked-documents': PayloadLockedDocumentsSelect<false> | PayloadLockedDocumentsSelect<true>;
    'payload-preferences': PayloadPreferencesSelect<false> | PayloadPreferencesSelect<true>;
    'payload-migrations': PayloadMigrationsSelect<false> | PayloadMigrationsSelect<true>;
  };
  db: {
    defaultIDType: number;
  };
  globals: {};
  globalsSelect: {};
  locale: null;
  user: User & {
    collection: 'users';
  };
  jobs: {
    tasks: unknown;
    workflows: unknown;
  };
}
export interface UserAuthOperations {
  forgotPassword: {
    email: string;
    password: string;
  };
  login: {
    email: string;
    password: string;
  };
  registerFirstUser: {
    email: string;
    password: string;
  };
  unlock: {
    email: string;
    password: string;
  };
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "users".
 */
export interface User {
  id: number;
  updatedAt: string;
  createdAt: string;
  email: string;
  resetPasswordToken?: string | null;
  resetPasswordExpiration?: string | null;
  salt?: string | null;
  hash?: string | null;
  loginAttempts?: number | null;
  lockUntil?: string | null;
  password?: string | null;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "media".
 */
export interface Media {
  id: number;
  alt: string;
  updatedAt: string;
  createdAt: string;
  url?: string | null;
  thumbnailURL?: string | null;
  filename?: string | null;
  mimeType?: string | null;
  filesize?: number | null;
  width?: number | null;
  height?: number | null;
  focalX?: number | null;
  focalY?: number | null;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "payload-locked-documents".
 */
export interface PayloadLockedDocument {
  id: number;
  document?:
    | ({
        relationTo: 'users';
        value: number | User;
      } | null)
    | ({
        relationTo: 'media';
        value: number | Media;
      } | null);
  globalSlug?: string | null;
  user: {
    relationTo: 'users';
    value: number | User;
  };
  updatedAt: string;
  createdAt: string;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "payload-preferences".
 */
export interface PayloadPreference {
  id: number;
  user: {
    relationTo: 'users';
    value: number | User;
  };
  key?: string | null;
  value?:
    | {
        [k: string]: unknown;
      }
    | unknown[]
    | string
    | number
    | boolean
    | null;
  updatedAt: string;
  createdAt: string;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "payload-migrations".
 */
export interface PayloadMigration {
  id: number;
  name?: string | null;
  batch?: number | null;
  updatedAt: string;
  createdAt: string;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "users_select".
 */
export interface UsersSelect<T extends boolean = true> {
  updatedAt?: T;
  createdAt?: T;
  email?: T;
  resetPasswordToken?: T;
  resetPasswordExpiration?: T;
  salt?: T;
  hash?: T;
  loginAttempts?: T;
  lockUntil?: T;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "media_select".
 */
export interface MediaSelect<T extends boolean = true> {
  alt?: T;
  updatedAt?: T;
  createdAt?: T;
  url?: T;
  thumbnailURL?: T;
  filename?: T;
  mimeType?: T;
  filesize?: T;
  width?: T;
  height?: T;
  focalX?: T;
  focalY?: T;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "payload-locked-documents_select".
 */
export interface PayloadLockedDocumentsSelect<T extends boolean = true> {
  document?: T;
  globalSlug?: T;
  user?: T;
  updatedAt?: T;
  createdAt?: T;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "payload-preferences_select".
 */
export interface PayloadPreferencesSelect<T extends boolean = true> {
  user?: T;
  key?: T;
  value?: T;
  updatedAt?: T;
  createdAt?: T;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "payload-migrations_select".
 */
export interface PayloadMigrationsSelect<T extends boolean = true> {
  name?: T;
  batch?: T;
  updatedAt?: T;
  createdAt?: T;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "auth".
 */
export interface Auth {
  [k: string]: unknown;
}


declare module 'payload' {
  export interface GeneratedTypes extends Config {}
}
```

## File: src/payload.config.ts
```typescript
import { postgresAdapter } from '@payloadcms/db-postgres'
import { lexicalEditor } from '@payloadcms/richtext-lexical'
import path from 'path'
import { buildConfig } from 'payload'
import { fileURLToPath } from 'url'
import sharp from 'sharp'

import { Users } from './collections/Users'
import { Media } from './collections/Media'

const filename = fileURLToPath(import.meta.url)
const dirname = path.dirname(filename)

export default buildConfig({
  admin: {
    user: Users.slug,
    importMap: {
      baseDir: path.resolve(dirname),
    },
  },
  collections: [Users, Media],
  editor: lexicalEditor(),
  secret: process.env.PAYLOAD_SECRET || '',
  typescript: {
    outputFile: path.resolve(dirname, 'payload-types.ts'),
  },
  db: postgresAdapter({
    pool: {
      connectionString: process.env.DATABASE_URI || '',
    },
  }),
  sharp,
  plugins: [],
})
```

## File: .env.example
```
DATABASE_URI=postgresql://127.0.0.1:5432/your-database-name
PAYLOAD_SECRET=YOUR_SECRET_HERE
```

## File: .gitignore
```
# See https://help.github.com/articles/ignoring-files/ for more about ignoring files.

# dependencies
/node_modules
/.pnp
.pnp.js
.yarn/install-state.gz

/.idea/*
!/.idea/runConfigurations

# testing
/coverage

# next.js
/.next/
/out/

# production
/build

# misc
.DS_Store
*.pem

# debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# local env files
.env*.local

# vercel
.vercel

# typescript
*.tsbuildinfo
next-env.d.ts

.env

/media
```

## File: .prettierrc.json
```json
{
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 100,
  "semi": false
}
```

## File: .yarnrc
```
--install.ignore-engines true
```

## File: docker-compose.yml
```yaml
version: '3'

services:
  payload:
    image: node:20-alpine
    ports:
      - '3000:3000'
    volumes:
      - .:/home/node/app
      - node_modules:/home/node/app/node_modules
    working_dir: /home/node/app/
    command: sh -c "corepack enable && corepack prepare pnpm@9.0.0 --activate && pnpm install && pnpm dev"
    depends_on:
      - postgres
      # - mongo
    env_file:
      - .env

  # Ensure your DATABASE_URI uses 'postgresql' as the hostname ie. postgresql://127.0.0.1:5432/your-database-name
  postgres:
    restart: always
    image: postgres:latest
    volumes:
      - pgdata:/var/lib/postgresql/data
    ports:
      - '5432:5432'
    environment:
      POSTGRES_USER: postgres
      POSTGRES_DB: your-database-name # THIS MUST MATCH YOUR DB NAME IN .env
      POSTGRES_HOST_AUTH_METHOD: trust

  # Uncomment the following to use mongodb
  # mongo:
  #   image: mongo:latest
  #   ports:
  #     - '27017:27017'
  #   command:
  #     - --storageEngine=wiredTiger
  #   volumes:
  #     - data:/data/db
  #   logging:
  #     driver: none

volumes:
  pgdata:
  # data:
  node_modules:
```

## File: Dockerfile
```dockerfile
# To use this Dockerfile, you have to set `output: 'standalone'` in your next.config.mjs file.
# From https://github.com/vercel/next.js/blob/canary/examples/with-docker/Dockerfile

FROM node:22.12.0-alpine AS base

# Install dependencies only when needed
FROM base AS deps
# Check https://github.com/nodejs/docker-node/tree/b4117f9333da4138b03a546ec926ef50a31506c3#nodealpine to understand why libc6-compat might be needed.
RUN apk add --no-cache libc6-compat
WORKDIR /app

# Install dependencies based on the preferred package manager
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./
RUN \
  if [ -f yarn.lock ]; then yarn --frozen-lockfile; \
  elif [ -f package-lock.json ]; then npm ci; \
  elif [ -f pnpm-lock.yaml ]; then corepack enable pnpm && pnpm i --frozen-lockfile; \
  else echo "Lockfile not found." && exit 1; \
  fi


# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Next.js collects completely anonymous telemetry data about general usage.
# Learn more here: https://nextjs.org/telemetry
# Uncomment the following line in case you want to disable telemetry during the build.
# ENV NEXT_TELEMETRY_DISABLED 1

RUN \
  if [ -f yarn.lock ]; then yarn run build; \
  elif [ -f package-lock.json ]; then npm run build; \
  elif [ -f pnpm-lock.yaml ]; then corepack enable pnpm && pnpm run build; \
  else echo "Lockfile not found." && exit 1; \
  fi

# Production image, copy all the files and run next
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production
# Uncomment the following line in case you want to disable telemetry during runtime.
# ENV NEXT_TELEMETRY_DISABLED 1

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# Remove this line if you do not have this folder
COPY --from=builder /app/public ./public

# Set the correct permission for prerender cache
RUN mkdir .next
RUN chown nextjs:nodejs .next

# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000

# server.js is created by next build from the standalone output
# https://nextjs.org/docs/pages/api-reference/next-config-js/output
CMD HOSTNAME="0.0.0.0" node server.js
```

## File: eslint.config.mjs
```
import { dirname } from 'path'
import { fileURLToPath } from 'url'
import { FlatCompat } from '@eslint/eslintrc'

const __filename = fileURLToPath(import.meta.url)
const __dirname = dirname(__filename)

const compat = new FlatCompat({
  baseDirectory: __dirname,
})

const eslintConfig = [
  ...compat.extends('next/core-web-vitals', 'next/typescript'),
  {
    rules: {
      '@typescript-eslint/ban-ts-comment': 'warn',
      '@typescript-eslint/no-empty-object-type': 'warn',
      '@typescript-eslint/no-explicit-any': 'warn',
      '@typescript-eslint/no-unused-vars': [
        'warn',
        {
          vars: 'all',
          args: 'after-used',
          ignoreRestSiblings: false,
          argsIgnorePattern: '^_',
          varsIgnorePattern: '^_',
          destructuredArrayIgnorePattern: '^_',
          caughtErrorsIgnorePattern: '^(_|ignore)',
        },
      ],
    },
  },
]

export default eslintConfig
```

## File: next.config.mjs
```
import { withPayload } from '@payloadcms/next/withPayload'

/** @type {import('next').NextConfig} */
const nextConfig = {
  // Your Next.js config here
}

export default withPayload(nextConfig)
```

## File: package.json
```json
{
  "name": "payload-postgres-template",
  "version": "1.0.0",
  "description": "A blank template to get started with Payload 3.0",
  "license": "MIT",
  "type": "module",
  "scripts": {
    "build": "cross-env NODE_OPTIONS=--no-deprecation next build",
    "dev": "cross-env NODE_OPTIONS=--no-deprecation next dev",
    "devsafe": "rm -rf .next && cross-env NODE_OPTIONS=--no-deprecation next dev",
    "generate:importmap": "cross-env NODE_OPTIONS=--no-deprecation payload generate:importmap",
    "generate:types": "cross-env NODE_OPTIONS=--no-deprecation payload generate:types",
    "lint": "cross-env NODE_OPTIONS=--no-deprecation next lint",
    "payload": "cross-env NODE_OPTIONS=--no-deprecation payload",
    "start": "cross-env NODE_OPTIONS=--no-deprecation next start",
    "ci": "payload migrate && pnpm build"
  },
  "dependencies": {
    "@payloadcms/next": "latest",
    "@payloadcms/payload-cloud": "latest",
    "@payloadcms/richtext-lexical": "latest",
    "cross-env": "^7.0.3",
    "graphql": "^16.8.1",
    "next": "15.2.0",
    "payload": "latest",
    "react": "19.0.0",
    "react-dom": "19.0.0",
    "sharp": "0.32.6",
    "@payloadcms/db-postgres": "latest"
  },
  "devDependencies": {
    "@eslint/eslintrc": "^3.2.0",
    "@types/node": "^22.5.4",
    "@types/react": "19.0.10",
    "@types/react-dom": "19.0.4",
    "eslint": "^9.16.0",
    "eslint-config-next": "15.2.0",
    "prettier": "^3.4.2",
    "typescript": "5.7.3"
  },
  "engines": {
    "node": "^18.20.2 || >=20.9.0",
    "pnpm": "^9"
  },
  "pnpm": {
    "onlyBuiltDependencies": [
      "sharp"
    ]
  }
}
```

## File: README.md
```markdown
# payload-postgres-template

payload-postgres-template

## Attributes

- **Database**: postgres
- **Storage Adapter**: localDisk
```

## File: tsconfig.json
```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "lib": [
      "DOM",
      "DOM.Iterable",
      "ES2022"
    ],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": [
        "./src/*"
      ],
      "@payload-config": [
        "./src/payload.config.ts"
      ]
    },
    "target": "ES2022",
  },
  "include": [
    "next-env.d.ts",
    "**/*.ts",
    "**/*.tsx",
    ".next/types/**/*.ts"
  ],
  "exclude": [
    "node_modules"
  ]
}
```
