This file is a merged representation of `payload/examples/multi-tenant/` codebase (https://github.com/payloadcms/payload/tree/main/examples/multi-tenant), combined into a single document by Repomix. This example demonstrates how to achieve a multi-tenancy in Payload. Tenants are separated by a `Tenants` collection.

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
  access/
    isSuperAdmin.ts
  app/
    (app)/
      tenant-domains/
        [tenant]/
          [...slug]/
            page.tsx
          login/
            page.tsx
          page.tsx
      tenant-slugs/
        [tenant]/
          [...slug]/
            page.tsx
          login/
            page.tsx
          page.tsx
      index.scss
      layout.tsx
      page.tsx
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
    components/
      Login/
        client.page.tsx
        index.scss
      RenderPage/
        index.tsx
  collections/
    Pages/
      access/
        superAdminOrTenantAdmin.ts
      hooks/
        ensureUniqueSlug.ts
      index.ts
    Tenants/
      access/
        byTenant.ts
        updateAndDelete.ts
      index.ts
    Users/
      access/
        create.ts
        isAccessingSelf.ts
        read.ts
        updateAndDelete.ts
      endpoints/
        externalUsersLogin.ts
      hooks/
        ensureUniqueUsername.ts
        setCookieBasedOnDomain.ts
      index.ts
  utilities/
    extractID.ts
    getCollectionIDType.ts
    getUserTenantIDs.ts
  payload-types.ts
  payload.config.ts
  seed.ts
.env.example
.eslintrc.cjs
.gitignore
.swcrc
next-env.d.ts
next.config.mjs
package.json
README.md
tsconfig.json
```

# Files

## File: src/access/isSuperAdmin.ts
````typescript
import type { Access } from 'payload'
import { User } from '../payload-types'

export const isSuperAdminAccess: Access = ({ req }): boolean => {
  return isSuperAdmin(req.user)
}

export const isSuperAdmin = (user: User | null): boolean => {
  return Boolean(user?.roles?.includes('super-admin'))
}
````

## File: src/app/(app)/tenant-domains/[tenant]/[...slug]/page.tsx
````typescript
import type { Where } from 'payload'

import configPromise from '@payload-config'
import { headers as getHeaders } from 'next/headers'
import { notFound, redirect } from 'next/navigation'
import { getPayload } from 'payload'
import React from 'react'

import { RenderPage } from '../../../../components/RenderPage'

// eslint-disable-next-line no-restricted-exports
export default async function Page({
  params: paramsPromise,
}: {
  params: Promise<{ slug?: string[]; tenant: string }>
}) {
  const params = await paramsPromise
  let slug = undefined
  if (params?.slug) {
    // remove the domain route param
    params.slug.splice(0, 1)
    slug = params.slug
  }

  const headers = await getHeaders()
  const payload = await getPayload({ config: configPromise })
  const { user } = await payload.auth({ headers })

  try {
    const tenantsQuery = await payload.find({
      collection: 'tenants',
      overrideAccess: false,
      user,
      where: {
        domain: {
          equals: params.tenant,
        },
      },
    })

    // If no tenant is found, the user does not have access
    // Show the login view
    if (tenantsQuery.docs.length === 0) {
      redirect(
        `/tenant-domains/login?redirect=${encodeURIComponent(
          `/tenant-domains${slug ? `/${slug.join('/')}` : ''}`,
        )}`,
      )
    }
  } catch (e) {
    // If the query fails, it means the user did not have access to query on the domain field
    // Show the login view
    redirect(
      `/tenant-domains/login?redirect=${encodeURIComponent(
        `/tenant-domains${slug ? `/${slug.join('/')}` : ''}`,
      )}`,
    )
  }

  const slugConstraint: Where = slug
    ? {
        slug: {
          equals: slug.join('/'),
        },
      }
    : {
        or: [
          {
            slug: {
              equals: '',
            },
          },
          {
            slug: {
              equals: 'home',
            },
          },
          {
            slug: {
              exists: false,
            },
          },
        ],
      }

  const pageQuery = await payload.find({
    collection: 'pages',
    overrideAccess: false,
    user,
    where: {
      and: [
        {
          'tenant.domain': {
            equals: params.tenant,
          },
        },
        slugConstraint,
      ],
    },
  })

  const pageData = pageQuery.docs?.[0]

  // The page with the provided slug could not be found
  if (!pageData) {
    return notFound()
  }

  // The page was found, render the page with data
  return <RenderPage data={pageData} />
}
````

## File: src/app/(app)/tenant-domains/[tenant]/login/page.tsx
````typescript
import React from 'react'

import { Login } from '../../../../components/Login/client.page'

type RouteParams = {
  tenant: string
}

// eslint-disable-next-line no-restricted-exports
export default async function Page({ params: paramsPromise }: { params: Promise<RouteParams> }) {
  const params = await paramsPromise

  return <Login tenantDomain={params.tenant} />
}
````

## File: src/app/(app)/tenant-domains/[tenant]/page.tsx
````typescript
import Page from './[...slug]/page'

export default Page
````

## File: src/app/(app)/tenant-slugs/[tenant]/[...slug]/page.tsx
````typescript
import type { Where } from 'payload'

import configPromise from '@payload-config'
import { headers as getHeaders } from 'next/headers'
import { notFound, redirect } from 'next/navigation'
import { getPayload } from 'payload'
import React from 'react'

import { RenderPage } from '../../../../components/RenderPage'

// eslint-disable-next-line no-restricted-exports
export default async function Page({
  params: paramsPromise,
}: {
  params: Promise<{ slug?: string[]; tenant: string }>
}) {
  const params = await paramsPromise

  const headers = await getHeaders()
  const payload = await getPayload({ config: configPromise })
  const { user } = await payload.auth({ headers })

  const slug = params?.slug

  try {
    const tenantsQuery = await payload.find({
      collection: 'tenants',
      overrideAccess: false,
      user,
      where: {
        slug: {
          equals: params.tenant,
        },
      },
    })
    // If no tenant is found, the user does not have access
    // Show the login view
    if (tenantsQuery.docs.length === 0) {
      redirect(
        `/tenant-slugs/${params.tenant}/login?redirect=${encodeURIComponent(
          `/tenant-slugs/${params.tenant}${slug ? `/${slug.join('/')}` : ''}`,
        )}`,
      )
    }
  } catch (e) {
    // If the query fails, it means the user did not have access to query on the slug field
    // Show the login view
    redirect(
      `/tenant-slugs/${params.tenant}/login?redirect=${encodeURIComponent(
        `/tenant-slugs/${params.tenant}${slug ? `/${slug.join('/')}` : ''}`,
      )}`,
    )
  }

  const slugConstraint: Where = slug
    ? {
        slug: {
          equals: slug.join('/'),
        },
      }
    : {
        or: [
          {
            slug: {
              equals: '',
            },
          },
          {
            slug: {
              equals: 'home',
            },
          },
          {
            slug: {
              exists: false,
            },
          },
        ],
      }

  const pageQuery = await payload.find({
    collection: 'pages',
    overrideAccess: false,
    user,
    where: {
      and: [
        {
          'tenant.slug': {
            equals: params.tenant,
          },
        },
        slugConstraint,
      ],
    },
  })

  const pageData = pageQuery.docs?.[0]

  // The page with the provided slug could not be found
  if (!pageData) {
    return notFound()
  }

  // The page was found, render the page with data
  return <RenderPage data={pageData} />
}
````

## File: src/app/(app)/tenant-slugs/[tenant]/login/page.tsx
````typescript
import React from 'react'

import { Login } from '../../../../components/Login/client.page'

type RouteParams = {
  tenant: string
}

// eslint-disable-next-line no-restricted-exports
export default async function Page({ params: paramsPromise }: { params: Promise<RouteParams> }) {
  const params = await paramsPromise

  return <Login tenantSlug={params.tenant} />
}
````

## File: src/app/(app)/tenant-slugs/[tenant]/page.tsx
````typescript
import Page from './[...slug]/page'

export default Page
````

## File: src/app/(app)/index.scss
````scss
.multi-tenant {
  body {
    margin: 0;
    padding: 10px;
  }
}
````

## File: src/app/(app)/layout.tsx
````typescript
import React from 'react'

import './index.scss'

const baseClass = 'multi-tenant'

export const metadata = {
  description: 'Generated by Next.js',
  title: 'Next.js',
}

// eslint-disable-next-line no-restricted-exports
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html className={baseClass} lang="en">
      <body>{children}</body>
    </html>
  )
}
````

## File: src/app/(app)/page.tsx
````typescript
export default async ({ params: paramsPromise }: { params: Promise<{ slug: string[] }> }) => {
  return (
    <div>
      <h1>Multi-Tenant Example</h1>
      <p>
        This multi-tenant example allows you to explore multi-tenancy with domains and with slugs.
      </p>

      <h2>Domains</h2>
      <p>When you visit a tenant by domain, the domain is used to determine the tenant.</p>
      <p>
        For example, visiting{' '}
        <a href="http://gold.localhost:3000/tenant-domains/login">
          http://gold.localhost:3000/tenant-domains/login
        </a>{' '}
        will show the tenant with the domain "gold.localhost".
      </p>

      <h2>Slugs</h2>
      <p>When you visit a tenant by slug, the slug is used to determine the tenant.</p>
      <p>
        For example, visiting{' '}
        <a href="http://localhost:3000/tenant-slugs/silver/login">
          http://localhost:3000/tenant-slugs/silver/login
        </a>{' '}
        will show the tenant with the slug "silver".
      </p>
    </div>
  )
}
````

## File: src/app/(payload)/admin/[[...segments]]/not-found.tsx
````typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import type { Metadata } from 'next'

import config from '@payload-config'
import { generatePageMetadata, NotFoundPage } from '@payloadcms/next/views'

import { importMap } from '../importMap.js'

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
  NotFoundPage({ config, importMap, params, searchParams })

export default NotFound
````

## File: src/app/(payload)/admin/[[...segments]]/page.tsx
````typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import type { Metadata } from 'next'

import config from '@payload-config'
import { generatePageMetadata, RootPage } from '@payloadcms/next/views'

import { importMap } from '../importMap.js'

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
  RootPage({ config, importMap, params, searchParams })

export default Page
````

## File: src/app/(payload)/admin/importMap.js
````javascript
import { TenantField as TenantField_1d0591e3cf4f332c83a86da13a0de59a } from '@payloadcms/plugin-multi-tenant/client'
import { TenantSelector as TenantSelector_1d0591e3cf4f332c83a86da13a0de59a } from '@payloadcms/plugin-multi-tenant/client'
import { TenantSelectionProvider as TenantSelectionProvider_d6d5f193a167989e2ee7d14202901e62 } from '@payloadcms/plugin-multi-tenant/rsc'

export const importMap = {
  "@payloadcms/plugin-multi-tenant/client#TenantField": TenantField_1d0591e3cf4f332c83a86da13a0de59a,
  "@payloadcms/plugin-multi-tenant/client#TenantSelector": TenantSelector_1d0591e3cf4f332c83a86da13a0de59a,
  "@payloadcms/plugin-multi-tenant/rsc#TenantSelectionProvider": TenantSelectionProvider_d6d5f193a167989e2ee7d14202901e62
}
````

## File: src/app/(payload)/api/[...slug]/route.ts
````typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import config from '@payload-config'
import { REST_DELETE, REST_GET, REST_OPTIONS, REST_PATCH, REST_POST } from '@payloadcms/next/routes'

export const GET = REST_GET(config)
export const POST = REST_POST(config)
export const DELETE = REST_DELETE(config)
export const PATCH = REST_PATCH(config)
export const OPTIONS = REST_OPTIONS(config)
````

## File: src/app/(payload)/api/graphql/route.ts
````typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import config from '@payload-config'
import { GRAPHQL_POST, REST_OPTIONS } from '@payloadcms/next/routes'

export const POST = GRAPHQL_POST(config)

export const OPTIONS = REST_OPTIONS(config)
````

## File: src/app/(payload)/api/graphql-playground/route.ts
````typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import config from '@payload-config'
import { GRAPHQL_PLAYGROUND_GET } from '@payloadcms/next/routes'

export const GET = GRAPHQL_PLAYGROUND_GET(config)
````

## File: src/app/(payload)/layout.tsx
````typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import type { ServerFunctionClient } from 'payload'

import config from '@payload-config'
import '@payloadcms/next/css'
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
````

## File: src/app/components/Login/client.page.tsx
````typescript
'use client'
import type { FormEvent } from 'react'

import { useRouter, useSearchParams } from 'next/navigation'
import React from 'react'

import './index.scss'

const baseClass = 'loginPage'

// go to /tenant1/home
// redirects to /tenant1/login?redirect=%2Ftenant1%2Fhome
// login, uses slug to set payload-tenant cookie

type Props = {
  tenantSlug?: string
  tenantDomain?: string
}
export const Login = ({ tenantSlug, tenantDomain }: Props) => {
  const usernameRef = React.useRef<HTMLInputElement>(null)
  const passwordRef = React.useRef<HTMLInputElement>(null)
  const router = useRouter()
  const searchParams = useSearchParams()

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault()
    if (!usernameRef?.current?.value || !passwordRef?.current?.value) {
      return
    }
    const actionRes = await fetch('/api/users/external-users/login', {
      body: JSON.stringify({
        password: passwordRef.current.value,
        tenantSlug,
        tenantDomain,
        username: usernameRef.current.value,
      }),
      headers: {
        'content-type': 'application/json',
      },
      method: 'post',
    })
    const json = await actionRes.json()

    if (actionRes.status === 200 && json.user) {
      const redirectTo = searchParams.get('redirect')
      if (redirectTo) {
        router.push(redirectTo)
        return
      } else {
        if (tenantDomain) {
          router.push('/tenant-domains')
        } else {
          router.push(`/tenant-slugs/${tenantSlug}`)
        }
      }
    } else if (actionRes.status === 400 && json?.errors?.[0]?.message) {
      window.alert(json.errors[0].message)
    } else {
      window.alert('Something went wrong, please try again.')
    }
  }

  return (
    <div className={baseClass}>
      <form onSubmit={handleSubmit}>
        <div>
          <label>
            Username
            <input name="username" ref={usernameRef} type="text" />
          </label>
        </div>
        <div>
          <label>
            Password
            <input name="password" ref={passwordRef} type="password" />
          </label>
        </div>

        <button type="submit">Login</button>
      </form>
    </div>
  )
}
````

## File: src/app/components/Login/index.scss
````scss
.loginPage {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;

  form {
    display: flex;
    align-items: flex-start;
    flex-direction: column;
    gap: 16px;
  }

  label {
    display: flex;
    flex-direction: column;
    gap: 4px;
  }

  input {
    padding: 8px 16px;
    width: 300px;
  }

  button {
    margin-top: 4px;
    padding: 8px 20px;
  }
}
````

## File: src/app/components/RenderPage/index.tsx
````typescript
import type { Page } from '@payload-types'

import React from 'react'

export const RenderPage = ({ data }: { data: Page }) => {
  return (
    <React.Fragment>
      <form action="/api/users/logout" method="post">
        <button type="submit">Logout</button>
      </form>
      <h2>Here you can decide how you would like to render the page data!</h2>

      <code>{JSON.stringify(data)}</code>
    </React.Fragment>
  )
}
````

## File: src/collections/Pages/access/superAdminOrTenantAdmin.ts
````typescript
import { getUserTenantIDs } from '@/utilities/getUserTenantIDs'
import { isSuperAdmin } from '../../../access/isSuperAdmin'
import { Access } from 'payload'

/**
 * Tenant admins and super admins can will be allowed access
 */
export const superAdminOrTenantAdminAccess: Access = ({ req }) => {
  if (!req.user) {
    return false
  }

  if (isSuperAdmin(req.user)) {
    return true
  }

  return {
    tenant: {
      in: getUserTenantIDs(req.user, 'tenant-admin'),
    },
  }
}
````

## File: src/collections/Pages/hooks/ensureUniqueSlug.ts
````typescript
import type { FieldHook, Where } from 'payload'

import { ValidationError } from 'payload'

import { getUserTenantIDs } from '../../../utilities/getUserTenantIDs'
import { extractID } from '@/utilities/extractID'

export const ensureUniqueSlug: FieldHook = async ({ data, originalDoc, req, value }) => {
  // if value is unchanged, skip validation
  if (originalDoc.slug === value) {
    return value
  }

  const constraints: Where[] = [
    {
      slug: {
        equals: value,
      },
    },
  ]

  const incomingTenantID = extractID(data?.tenant)
  const currentTenantID = extractID(originalDoc?.tenant)
  const tenantIDToMatch = incomingTenantID || currentTenantID

  if (tenantIDToMatch) {
    constraints.push({
      tenant: {
        equals: tenantIDToMatch,
      },
    })
  }

  const findDuplicatePages = await req.payload.find({
    collection: 'pages',
    where: {
      and: constraints,
    },
  })

  if (findDuplicatePages.docs.length > 0 && req.user) {
    const tenantIDs = getUserTenantIDs(req.user)
    // if the user is an admin or has access to more than 1 tenant
    // provide a more specific error message
    if (req.user.roles?.includes('super-admin') || tenantIDs.length > 1) {
      const attemptedTenantChange = await req.payload.findByID({
        id: tenantIDToMatch,
        collection: 'tenants',
      })

      throw new ValidationError({
        errors: [
          {
            message: `The "${attemptedTenantChange.name}" tenant already has a page with the slug "${value}". Slugs must be unique per tenant.`,
            path: 'slug',
          },
        ],
      })
    }

    throw new ValidationError({
      errors: [
        {
          message: `A page with the slug ${value} already exists. Slug must be unique per tenant.`,
          path: 'slug',
        },
      ],
    })
  }

  return value
}
````

## File: src/collections/Pages/index.ts
````typescript
import type { CollectionConfig } from 'payload'

import { ensureUniqueSlug } from './hooks/ensureUniqueSlug'
import { superAdminOrTenantAdminAccess } from '@/collections/Pages/access/superAdminOrTenantAdmin'

export const Pages: CollectionConfig = {
  slug: 'pages',
  access: {
    create: superAdminOrTenantAdminAccess,
    delete: superAdminOrTenantAdminAccess,
    read: () => true,
    update: superAdminOrTenantAdminAccess,
  },
  admin: {
    useAsTitle: 'title',
  },
  fields: [
    {
      name: 'title',
      type: 'text',
    },
    {
      name: 'slug',
      type: 'text',
      defaultValue: 'home',
      hooks: {
        beforeValidate: [ensureUniqueSlug],
      },
      index: true,
    },
  ],
}
````

## File: src/collections/Tenants/access/byTenant.ts
````typescript
import type { Access } from 'payload'

import { isSuperAdmin } from '../../../access/isSuperAdmin'

export const filterByTenantRead: Access = (args) => {
  // Allow public tenants to be read by anyone
  if (!args.req.user) {
    return {
      allowPublicRead: {
        equals: true,
      },
    }
  }

  return true
}

export const canMutateTenant: Access = ({ req }) => {
  if (!req.user) {
    return false
  }

  if (isSuperAdmin(req.user)) {
    return true
  }

  return {
    id: {
      in:
        req.user?.tenants
          ?.map(({ roles, tenant }) =>
            roles?.includes('tenant-admin')
              ? tenant && (typeof tenant === 'string' ? tenant : tenant.id)
              : null,
          )
          .filter(Boolean) || [],
    },
  }
}
````

## File: src/collections/Tenants/access/updateAndDelete.ts
````typescript
import { isSuperAdmin } from '@/access/isSuperAdmin'
import { getUserTenantIDs } from '@/utilities/getUserTenantIDs'
import { Access } from 'payload'

export const updateAndDeleteAccess: Access = ({ req }) => {
  if (!req.user) {
    return false
  }

  if (isSuperAdmin(req.user)) {
    return true
  }

  return {
    id: {
      in: getUserTenantIDs(req.user, 'tenant-admin'),
    },
  }
}
````

## File: src/collections/Tenants/index.ts
````typescript
import type { CollectionConfig } from 'payload'

import { isSuperAdminAccess } from '@/access/isSuperAdmin'
import { updateAndDeleteAccess } from './access/updateAndDelete'

export const Tenants: CollectionConfig = {
  slug: 'tenants',
  access: {
    create: isSuperAdminAccess,
    delete: updateAndDeleteAccess,
    read: ({ req }) => Boolean(req.user),
    update: updateAndDeleteAccess,
  },
  admin: {
    useAsTitle: 'name',
  },
  fields: [
    {
      name: 'name',
      type: 'text',
      required: true,
    },
    {
      name: 'domain',
      type: 'text',
      admin: {
        description: 'Used for domain-based tenant handling',
      },
    },
    {
      name: 'slug',
      type: 'text',
      admin: {
        description: 'Used for url paths, example: /tenant-slug/page-slug',
      },
      index: true,
      required: true,
    },
    {
      name: 'allowPublicRead',
      type: 'checkbox',
      admin: {
        description:
          'If checked, logging in is not required to read. Useful for building public pages.',
        position: 'sidebar',
      },
      defaultValue: false,
      index: true,
    },
  ],
}
````

## File: src/collections/Users/access/create.ts
````typescript
import type { Access } from 'payload'

import type { User } from '../../../payload-types'

import { isSuperAdmin } from '../../../access/isSuperAdmin'
import { getUserTenantIDs } from '../../../utilities/getUserTenantIDs'

export const createAccess: Access<User> = ({ req }) => {
  if (!req.user) {
    return false
  }

  if (isSuperAdmin(req.user)) {
    return true
  }

  const adminTenantAccessIDs = getUserTenantIDs(req.user, 'tenant-admin')

  if (adminTenantAccessIDs.length) {
    return true
  }

  return false
}
````

## File: src/collections/Users/access/isAccessingSelf.ts
````typescript
import { User } from '@/payload-types'

export const isAccessingSelf = ({ id, user }: { user?: User; id?: string | number }): boolean => {
  return user ? Boolean(user.id === id) : false
}
````

## File: src/collections/Users/access/read.ts
````typescript
import type { User } from '@/payload-types'
import type { Access, Where } from 'payload'
import { getTenantFromCookie } from '@payloadcms/plugin-multi-tenant/utilities'

import { isSuperAdmin } from '../../../access/isSuperAdmin'
import { getUserTenantIDs } from '../../../utilities/getUserTenantIDs'
import { isAccessingSelf } from './isAccessingSelf'
import { getCollectionIDType } from '@/utilities/getCollectionIDType'

export const readAccess: Access<User> = ({ req, id }) => {
  if (!req?.user) {
    return false
  }

  if (isAccessingSelf({ id, user: req.user })) {
    return true
  }

  const superAdmin = isSuperAdmin(req.user)
  const selectedTenant = getTenantFromCookie(
    req.headers,
    getCollectionIDType({ payload: req.payload, collectionSlug: 'tenants' }),
  )
  const adminTenantAccessIDs = getUserTenantIDs(req.user, 'tenant-admin')

  if (selectedTenant) {
    // If it's a super admin, or they have access to the tenant ID set in cookie
    const hasTenantAccess = adminTenantAccessIDs.some((id) => id === selectedTenant)
    if (superAdmin || hasTenantAccess) {
      return {
        'tenants.tenant': {
          equals: selectedTenant,
        },
      }
    }
  }

  if (superAdmin) {
    return true
  }

  return {
    or: [
      {
        id: {
          equals: req.user.id,
        },
      },
      {
        'tenants.tenant': {
          in: adminTenantAccessIDs,
        },
      },
    ],
  } as Where
}
````

## File: src/collections/Users/access/updateAndDelete.ts
````typescript
import type { Access } from 'payload'

import { getUserTenantIDs } from '../../../utilities/getUserTenantIDs'
import { isSuperAdmin } from '@/access/isSuperAdmin'
import { isAccessingSelf } from './isAccessingSelf'

export const updateAndDeleteAccess: Access = ({ req, id }) => {
  const { user } = req

  if (!user) {
    return false
  }

  if (isSuperAdmin(user) || isAccessingSelf({ user, id })) {
    return true
  }

  /**
   * Constrains update and delete access to users that belong
   * to the same tenant as the tenant-admin making the request
   *
   * You may want to take this a step further with a beforeChange
   * hook to ensure that the a tenant-admin can only remove users
   * from their own tenant in the tenants array.
   */
  return {
    'tenants.tenant': {
      in: getUserTenantIDs(user, 'tenant-admin'),
    },
  }
}
````

## File: src/collections/Users/endpoints/externalUsersLogin.ts
````typescript
import type { Collection, Endpoint } from 'payload'

import { headersWithCors } from '@payloadcms/next/utilities'
import { APIError, generatePayloadCookie } from 'payload'

// A custom endpoint that can be reached by POST request
// at: /api/users/external-users/login
export const externalUsersLogin: Endpoint = {
  handler: async (req) => {
    let data: { [key: string]: string } = {}

    try {
      if (typeof req.json === 'function') {
        data = await req.json()
      }
    } catch (error) {
      // swallow error, data is already empty object
    }
    const { password, tenantSlug, tenantDomain, username } = data

    if (!username || !password) {
      throw new APIError('Username and Password are required for login.', 400, null, true)
    }

    const fullTenant = (
      await req.payload.find({
        collection: 'tenants',
        where: tenantDomain
          ? {
              domain: {
                equals: tenantDomain,
              },
            }
          : {
              slug: {
                equals: tenantSlug,
              },
            },
      })
    ).docs[0]

    const foundUser = await req.payload.find({
      collection: 'users',
      where: {
        or: [
          {
            and: [
              {
                email: {
                  equals: username,
                },
              },
              {
                'tenants.tenant': {
                  equals: fullTenant.id,
                },
              },
            ],
          },
          {
            and: [
              {
                username: {
                  equals: username,
                },
              },
              {
                'tenants.tenant': {
                  equals: fullTenant.id,
                },
              },
            ],
          },
        ],
      },
    })

    if (foundUser.totalDocs > 0) {
      try {
        const loginAttempt = await req.payload.login({
          collection: 'users',
          data: {
            email: foundUser.docs[0].email,
            password,
          },
          req,
        })

        if (loginAttempt?.token) {
          const collection: Collection = (req.payload.collections as { [key: string]: Collection })[
            'users'
          ]
          const cookie = generatePayloadCookie({
            collectionAuthConfig: collection.config.auth,
            cookiePrefix: req.payload.config.cookiePrefix,
            token: loginAttempt.token,
          })

          return Response.json(loginAttempt, {
            headers: headersWithCors({
              headers: new Headers({
                'Set-Cookie': cookie,
              }),
              req,
            }),
            status: 200,
          })
        }

        throw new APIError(
          'Unable to login with the provided username and password.',
          400,
          null,
          true,
        )
      } catch (e) {
        throw new APIError(
          'Unable to login with the provided username and password.',
          400,
          null,
          true,
        )
      }
    }

    throw new APIError('Unable to login with the provided username and password.', 400, null, true)
  },
  method: 'post',
  path: '/external-users/login',
}
````

## File: src/collections/Users/hooks/ensureUniqueUsername.ts
````typescript
import type { FieldHook, Where } from 'payload'

import { ValidationError } from 'payload'

import { getUserTenantIDs } from '../../../utilities/getUserTenantIDs'
import { extractID } from '@/utilities/extractID'
import { getTenantFromCookie } from '@payloadcms/plugin-multi-tenant/utilities'
import { getCollectionIDType } from '@/utilities/getCollectionIDType'

export const ensureUniqueUsername: FieldHook = async ({ data, originalDoc, req, value }) => {
  // if value is unchanged, skip validation
  if (originalDoc.username === value) {
    return value
  }

  const constraints: Where[] = [
    {
      username: {
        equals: value,
      },
    },
  ]

  const selectedTenant = getTenantFromCookie(
    req.headers,
    getCollectionIDType({ payload: req.payload, collectionSlug: 'tenants' }),
  )

  if (selectedTenant) {
    constraints.push({
      'tenants.tenant': {
        equals: selectedTenant,
      },
    })
  }

  const findDuplicateUsers = await req.payload.find({
    collection: 'users',
    where: {
      and: constraints,
    },
  })

  if (findDuplicateUsers.docs.length > 0 && req.user) {
    const tenantIDs = getUserTenantIDs(req.user)
    // if the user is an admin or has access to more than 1 tenant
    // provide a more specific error message
    if (req.user.roles?.includes('super-admin') || tenantIDs.length > 1) {
      const attemptedTenantChange = await req.payload.findByID({
        // @ts-ignore - selectedTenant will match DB ID type
        id: selectedTenant,
        collection: 'tenants',
      })

      throw new ValidationError({
        errors: [
          {
            message: `The "${attemptedTenantChange.name}" tenant already has a user with the username "${value}". Usernames must be unique per tenant.`,
            path: 'username',
          },
        ],
      })
    }

    throw new ValidationError({
      errors: [
        {
          message: `A user with the username ${value} already exists. Usernames must be unique per tenant.`,
          path: 'username',
        },
      ],
    })
  }

  return value
}
````

## File: src/collections/Users/hooks/setCookieBasedOnDomain.ts
````typescript
import type { CollectionAfterLoginHook } from 'payload'

import { mergeHeaders, generateCookie, getCookieExpiration } from 'payload'

export const setCookieBasedOnDomain: CollectionAfterLoginHook = async ({ req, user }) => {
  const relatedOrg = await req.payload.find({
    collection: 'tenants',
    depth: 0,
    limit: 1,
    where: {
      domain: {
        equals: req.headers.get('host'),
      },
    },
  })

  // If a matching tenant is found, set the 'payload-tenant' cookie
  if (relatedOrg && relatedOrg.docs.length > 0) {
    const tenantCookie = generateCookie({
      name: 'payload-tenant',
      expires: getCookieExpiration({ seconds: 7200 }),
      path: '/',
      returnCookieAsObject: false,
      value: String(relatedOrg.docs[0].id),
    })

    // Merge existing responseHeaders with the new Set-Cookie header
    const newHeaders = new Headers({
      'Set-Cookie': tenantCookie as string,
    })

    // Ensure you merge existing response headers if they already exist
    req.responseHeaders = req.responseHeaders
      ? mergeHeaders(req.responseHeaders, newHeaders)
      : newHeaders
  }

  return user
}
````

## File: src/collections/Users/index.ts
````typescript
import type { CollectionConfig } from 'payload'

import { createAccess } from './access/create'
import { readAccess } from './access/read'
import { updateAndDeleteAccess } from './access/updateAndDelete'
import { externalUsersLogin } from './endpoints/externalUsersLogin'
import { ensureUniqueUsername } from './hooks/ensureUniqueUsername'
import { isSuperAdmin } from '@/access/isSuperAdmin'
import { setCookieBasedOnDomain } from './hooks/setCookieBasedOnDomain'
import { tenantsArrayField } from '@payloadcms/plugin-multi-tenant/fields'

const defaultTenantArrayField = tenantsArrayField({
  tenantsArrayFieldName: 'tenants',
  tenantsArrayTenantFieldName: 'tenant',
  tenantsCollectionSlug: 'tenants',
  arrayFieldAccess: {},
  tenantFieldAccess: {},
  rowFields: [
    {
      name: 'roles',
      type: 'select',
      defaultValue: ['tenant-viewer'],
      hasMany: true,
      options: ['tenant-admin', 'tenant-viewer'],
      required: true,
    },
  ],
})

const Users: CollectionConfig = {
  slug: 'users',
  access: {
    create: createAccess,
    delete: updateAndDeleteAccess,
    read: readAccess,
    update: updateAndDeleteAccess,
  },
  admin: {
    useAsTitle: 'email',
  },
  auth: true,
  endpoints: [externalUsersLogin],
  fields: [
    {
      admin: {
        position: 'sidebar',
      },
      name: 'roles',
      type: 'select',
      defaultValue: ['user'],
      hasMany: true,
      options: ['super-admin', 'user'],
      access: {
        update: ({ req }) => {
          return isSuperAdmin(req.user)
        },
      },
    },
    {
      name: 'username',
      type: 'text',
      hooks: {
        beforeValidate: [ensureUniqueUsername],
      },
      index: true,
    },
    {
      ...defaultTenantArrayField,
      admin: {
        ...(defaultTenantArrayField?.admin || {}),
        position: 'sidebar',
      },
    },
  ],
  // The following hook sets a cookie based on the domain a user logs in from.
  // It checks the domain and matches it to a tenant in the system, then sets
  // a 'payload-tenant' cookie for that tenant.

  hooks: {
    afterLogin: [setCookieBasedOnDomain],
  },
}

export default Users
````

## File: src/utilities/extractID.ts
````typescript
import { Config } from '@/payload-types'
import type { CollectionSlug } from 'payload'

export const extractID = <T extends Config['collections'][CollectionSlug]>(
  objectOrID: T | T['id'],
): T['id'] => {
  if (objectOrID && typeof objectOrID === 'object') return objectOrID.id

  return objectOrID
}
````

## File: src/utilities/getCollectionIDType.ts
````typescript
import type { CollectionSlug, Payload } from 'payload'

type Args = {
  collectionSlug: CollectionSlug
  payload: Payload
}
export const getCollectionIDType = ({ collectionSlug, payload }: Args): 'number' | 'text' => {
  return payload.collections[collectionSlug]?.customIDType ?? payload.db.defaultIDType
}
````

## File: src/utilities/getUserTenantIDs.ts
````typescript
import type { Tenant, User } from '../payload-types'
import { extractID } from './extractID'

/**
 * Returns array of all tenant IDs assigned to a user
 *
 * @param user - User object with tenants field
 * @param role - Optional role to filter by
 */
export const getUserTenantIDs = (
  user: null | User,
  role?: NonNullable<User['tenants']>[number]['roles'][number],
): Tenant['id'][] => {
  if (!user) {
    return []
  }

  return (
    user?.tenants?.reduce<Tenant['id'][]>((acc, { roles, tenant }) => {
      if (role && !roles.includes(role)) {
        return acc
      }

      if (tenant) {
        acc.push(extractID(tenant))
      }

      return acc
    }, []) || []
  )
}
````

## File: src/payload-types.ts
````typescript
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
    pages: Page;
    users: User;
    tenants: Tenant;
    'payload-locked-documents': PayloadLockedDocument;
    'payload-preferences': PayloadPreference;
    'payload-migrations': PayloadMigration;
  };
  collectionsJoins: {};
  collectionsSelect: {
    pages: PagesSelect<false> | PagesSelect<true>;
    users: UsersSelect<false> | UsersSelect<true>;
    tenants: TenantsSelect<false> | TenantsSelect<true>;
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
 * via the `definition` "pages".
 */
export interface Page {
  id: number;
  tenant?: (number | null) | Tenant;
  title?: string | null;
  slug?: string | null;
  updatedAt: string;
  createdAt: string;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "tenants".
 */
export interface Tenant {
  id: number;
  name: string;
  /**
   * Used for domain-based tenant handling
   */
  domain?: string | null;
  /**
   * Used for url paths, example: /tenant-slug/page-slug
   */
  slug: string;
  /**
   * If checked, logging in is not required to read. Useful for building public pages.
   */
  allowPublicRead?: boolean | null;
  updatedAt: string;
  createdAt: string;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "users".
 */
export interface User {
  id: number;
  roles?: ('super-admin' | 'user')[] | null;
  username?: string | null;
  tenants?:
    | {
        tenant: number | Tenant;
        roles: ('tenant-admin' | 'tenant-viewer')[];
        id?: string | null;
      }[]
    | null;
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
 * via the `definition` "payload-locked-documents".
 */
export interface PayloadLockedDocument {
  id: number;
  document?:
    | ({
        relationTo: 'pages';
        value: number | Page;
      } | null)
    | ({
        relationTo: 'users';
        value: number | User;
      } | null)
    | ({
        relationTo: 'tenants';
        value: number | Tenant;
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
 * via the `definition` "pages_select".
 */
export interface PagesSelect<T extends boolean = true> {
  tenant?: T;
  title?: T;
  slug?: T;
  updatedAt?: T;
  createdAt?: T;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "users_select".
 */
export interface UsersSelect<T extends boolean = true> {
  roles?: T;
  username?: T;
  tenants?:
    | T
    | {
        tenant?: T;
        roles?: T;
        id?: T;
      };
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
 * via the `definition` "tenants_select".
 */
export interface TenantsSelect<T extends boolean = true> {
  name?: T;
  domain?: T;
  slug?: T;
  allowPublicRead?: T;
  updatedAt?: T;
  createdAt?: T;
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
````

## File: src/payload.config.ts
````typescript
import { mongooseAdapter } from '@payloadcms/db-mongodb'
import { postgresAdapter } from '@payloadcms/db-postgres'
import { lexicalEditor } from '@payloadcms/richtext-lexical'
import path from 'path'
import { buildConfig } from 'payload'
import { fileURLToPath } from 'url'

import { Pages } from './collections/Pages'
import { Tenants } from './collections/Tenants'
import Users from './collections/Users'
import { multiTenantPlugin } from '@payloadcms/plugin-multi-tenant'
import { isSuperAdmin } from './access/isSuperAdmin'
import type { Config } from './payload-types'
import { getUserTenantIDs } from './utilities/getUserTenantIDs'
import { seed } from './seed'

const filename = fileURLToPath(import.meta.url)
const dirname = path.dirname(filename)

// eslint-disable-next-line no-restricted-exports
export default buildConfig({
  admin: {
    user: 'users',
  },
  collections: [Pages, Users, Tenants],
  // db: mongooseAdapter({
  //   url: process.env.DATABASE_URI as string,
  // }),
  db: postgresAdapter({
    pool: {
      connectionString: process.env.POSTGRES_URL,
    },
  }),
  onInit: async (args) => {
    if (process.env.SEED_DB) {
      await seed(args)
    }
  },
  editor: lexicalEditor({}),
  graphQL: {
    schemaOutputFile: path.resolve(dirname, 'generated-schema.graphql'),
  },
  secret: process.env.PAYLOAD_SECRET as string,
  typescript: {
    outputFile: path.resolve(dirname, 'payload-types.ts'),
  },
  plugins: [
    multiTenantPlugin<Config>({
      collections: {
        pages: {},
      },
      tenantField: {
        access: {
          read: () => true,
          update: ({ req }) => {
            if (isSuperAdmin(req.user)) {
              return true
            }
            return getUserTenantIDs(req.user).length > 0
          },
        },
      },
      tenantsArrayField: {
        includeDefaultField: false,
      },
      userHasAccessToAllTenants: (user) => isSuperAdmin(user),
    }),
  ],
})
````

## File: src/seed.ts
````typescript
import { Config } from 'payload'

export const seed: NonNullable<Config['onInit']> = async (payload): Promise<void> => {
  const tenant1 = await payload.create({
    collection: 'tenants',
    data: {
      name: 'Tenant 1',
      slug: 'gold',
      domain: 'gold.localhost',
    },
  })

  const tenant2 = await payload.create({
    collection: 'tenants',
    data: {
      name: 'Tenant 2',
      slug: 'silver',
      domain: 'silver.localhost',
    },
  })

  const tenant3 = await payload.create({
    collection: 'tenants',
    data: {
      name: 'Tenant 3',
      slug: 'bronze',
      domain: 'bronze.localhost',
    },
  })

  await payload.create({
    collection: 'users',
    data: {
      email: 'demo@payloadcms.com',
      password: 'demo',
      roles: ['super-admin'],
    },
  })

  await payload.create({
    collection: 'users',
    data: {
      email: 'tenant1@payloadcms.com',
      password: 'demo',
      tenants: [
        {
          roles: ['tenant-admin'],
          tenant: tenant1.id,
        },
      ],
      username: 'tenant1',
    },
  })

  await payload.create({
    collection: 'users',
    data: {
      email: 'tenant2@payloadcms.com',
      password: 'demo',
      tenants: [
        {
          roles: ['tenant-admin'],
          tenant: tenant2.id,
        },
      ],
      username: 'tenant2',
    },
  })

  await payload.create({
    collection: 'users',
    data: {
      email: 'tenant3@payloadcms.com',
      password: 'demo',
      tenants: [
        {
          roles: ['tenant-admin'],
          tenant: tenant3.id,
        },
      ],
      username: 'tenant3',
    },
  })

  await payload.create({
    collection: 'users',
    data: {
      email: 'multi-admin@payloadcms.com',
      password: 'demo',
      tenants: [
        {
          roles: ['tenant-admin'],
          tenant: tenant1.id,
        },
        {
          roles: ['tenant-admin'],
          tenant: tenant2.id,
        },
        {
          roles: ['tenant-admin'],
          tenant: tenant3.id,
        },
      ],
      username: 'multi-admin',
    },
  })

  await payload.create({
    collection: 'pages',
    data: {
      slug: 'home',
      tenant: tenant1.id,
      title: 'Page for Tenant 1',
    },
  })

  await payload.create({
    collection: 'pages',
    data: {
      slug: 'home',
      tenant: tenant2.id,
      title: 'Page for Tenant 2',
    },
  })

  await payload.create({
    collection: 'pages',
    data: {
      slug: 'home',
      tenant: tenant3.id,
      title: 'Page for Tenant 3',
    },
  })
}
````

## File: .env.example
````
DATABASE_URI=mongodb://127.0.0.1/payload-example-multi-tenant
POSTGRES_URL=postgres://127.0.0.1:5432/payload-example-multi-tenant
PAYLOAD_SECRET=PAYLOAD_MULTI_TENANT_EXAMPLE_SECRET_KEY
PAYLOAD_PUBLIC_SERVER_URL=http://localhost:3000
SEED_DB=true
````

## File: .eslintrc.cjs
````
module.exports = {
  root: true,
  extends: ['@payloadcms'],
}
````

## File: .gitignore
````
build
dist
node_modules
package-lock.json
.env
````

## File: .swcrc
````
{
  "$schema": "https://json.schemastore.org/swcrc",
  "sourceMaps": true,
  "jsc": {
    "target": "esnext",
    "parser": {
      "syntax": "typescript",
      "tsx": true,
      "dts": true
    },
    "transform": {
      "react": {
        "runtime": "automatic",
        "pragmaFrag": "React.Fragment",
        "throwIfNamespace": true,
        "development": false,
        "useBuiltins": true
      }
    }
  },
  "module": {
    "type": "es6"
  }
}
````

## File: next-env.d.ts
````typescript
/// <reference types="next" />
/// <reference types="next/image-types/global" />

// NOTE: This file should not be edited
// see https://nextjs.org/docs/app/api-reference/config/typescript for more information.
````

## File: next.config.mjs
````
import { withPayload } from '@payloadcms/next/withPayload'

/** @type {import('next').NextConfig} */
const nextConfig = {
  // Your Next.js config here
  async rewrites() {
    return [
      {
        source: '/((?!admin|api))tenant-domains/:path*',
        destination: '/tenant-domains/:tenant/:path*',
        has: [
          {
            type: 'host',
            value: '(?<tenant>.*)',
          },
        ],
      },
    ]
  },
}

export default withPayload(nextConfig)
````

## File: package.json
````json
{
  "name": "multi-tenant",
  "version": "1.0.0",
  "description": "An example of a multi tenant application with Payload",
  "license": "MIT",
  "type": "module",
  "scripts": {
    "_dev": "cross-env NODE_OPTIONS=--no-deprecation next dev",
    "build": "cross-env NODE_OPTIONS=--no-deprecation next build",
    "dev": "cross-env NODE_OPTIONS=--no-deprecation && pnpm seed && next dev",
    "generate:importmap": "cross-env NODE_OPTIONS=--no-deprecation payload generate:importmap",
    "generate:schema": "payload-graphql generate:schema",
    "generate:types": "payload generate:types",
    "payload": "cross-env NODE_OPTIONS=--no-deprecation payload",
    "seed": "npm run payload migrate:fresh",
    "start": "cross-env NODE_OPTIONS=--no-deprecation next start"
  },
  "dependencies": {
    "@payloadcms/db-mongodb": "latest",
    "@payloadcms/db-postgres": "^3.25.0",
    "@payloadcms/next": "latest",
    "@payloadcms/plugin-multi-tenant": "latest",
    "@payloadcms/richtext-lexical": "latest",
    "@payloadcms/ui": "latest",
    "cross-env": "^7.0.3",
    "dotenv": "^8.2.0",
    "graphql": "^16.9.0",
    "next": "^15.0.0",
    "payload": "latest",
    "qs-esm": "7.0.2",
    "react": "19.0.0",
    "react-dom": "19.0.0",
    "sharp": "0.32.6"
  },
  "devDependencies": {
    "@payloadcms/graphql": "latest",
    "@swc/core": "^1.6.13",
    "@types/react": "19.0.1",
    "@types/react-dom": "19.0.1",
    "eslint": "^8.57.0",
    "eslint-config-next": "^15.0.0",
    "tsx": "^4.16.2",
    "typescript": "5.5.2"
  },
  "engines": {
    "node": "^18.20.2 || >=20.9.0"
  }
}
````

## File: README.md
````markdown
# Payload Multi-Tenant Example

This example demonstrates how to achieve a multi-tenancy in [Payload](https://github.com/payloadcms/payload). Tenants are separated by a `Tenants` collection.

## Quick Start

To spin up this example locally, follow these steps:

1. Run the following command to create a project from the example:

- `npx create-payload-app --example multi-tenant`

2. `cp .env.example .env` to copy the example environment variables

3. `pnpm dev`, `yarn dev` or `npm run dev` to start the server
   - Press `y` when prompted to seed the database
4. `open http://localhost:3000` to access the home page
5. `open http://localhost:3000/admin` to access the admin panel

### Default users

The seed script seeds 3 tenants.
Login with email `demo@payloadcms.com` and password `demo`

## How it works

A multi-tenant Payload application is a single server that hosts multiple "tenants". Examples of tenants may be your agency's clients, your business conglomerate's organizations, or your SaaS customers.

Each tenant has its own set of users, pages, and other data that is scoped to that tenant. This means that your application will be shared across tenants but the data will be scoped to each tenant.

### Collections

See the [Collections](https://payloadcms.com/docs/configuration/collections) docs for details on how to extend any of this functionality.

- #### Users

  The `users` collection is auth-enabled and encompasses both app-wide and tenant-scoped users based on the value of their `roles` and `tenants` fields. Users with the role `super-admin` can manage your entire application, while users with the _tenant role_ of `admin` have limited access to the platform and can manage only the tenant(s) they are assigned to, see [Tenants](#tenants) for more details.

  For additional help with authentication, see the official [Auth Example](https://github.com/payloadcms/payload/tree/main/examples/cms#readme) or the [Authentication](https://payloadcms.com/docs/authentication/overview#authentication-overview) docs.

- #### Tenants

  A `tenants` collection is used to achieve tenant-based access control. Each user is assigned an array of `tenants` which includes a relationship to a `tenant` and their `roles` within that tenant. You can then scope any document within your application to any of your tenants using a simple [relationship](https://payloadcms.com/docs/fields/relationship) field on the `users` or `pages` collections, or any other collection that your application needs. The value of this field is used to filter documents in the admin panel and API to ensure that users can only access documents that belong to their tenant and are within their role. See [Access Control](#access-control) for more details.

  For more details on how to extend this functionality, see the [Payload Access Control](https://payloadcms.com/docs/access-control/overview) docs.

  **Domain-based Tenant Setting**:

  This example also supports domain-based tenant selection, where tenants can be associated with a specific domain. If a tenant is associated with a domain (e.g., `gold.localhost:3000`), when a user logs in from that domain, they will be automatically scoped to the matching tenant. This is accomplished through an optional `afterLogin` hook that sets a `payload-tenant` cookie based on the domain.

For the domain portion of the example to function properly, you will need to add the following entries to your system's `/etc/hosts` file:

```
127.0.0.1 gold.localhost silver.localhost bronze.localhost
```

- #### Pages

  Each page is assigned a `tenant`, which is used to control access and scope API requests. Only users with the `super-admin` role can create pages, and pages are assigned to specific tenants. Other users can view only the pages assigned to the tenant they are associated with.

## Access control

Basic role-based access control is set up to determine what users can and cannot do based on their roles, which are:

- `super-admin`: They can access the Payload admin panel to manage your multi-tenant application. They can see all tenants and make all operations.
- `user`: They can only access the Payload admin panel if they are a tenant-admin, in which case they have a limited access to operations based on their tenant (see below).

This applies to each collection in the following ways:

- `users`: Only super-admins, tenant-admins, and the user themselves can access their profile. Anyone can create a user, but only these admins can delete users. See [Users](#users) for more details.
- `tenants`: Only super-admins and tenant-admins can read, create, update, or delete tenants. See [Tenants](#tenants) for more details.
- `pages`: Everyone can access pages, but only super-admins and tenant-admins can create, update, or delete them.

> If you have versions and drafts enabled on your pages, you will need to add additional read access control condition to check the user's tenants that prevents them from accessing draft documents of other tenants.

For more details on how to extend this functionality, see the [Payload Access Control](https://payloadcms.com/docs/access-control/overview#access-control) docs.

## CORS

This multi-tenant setup requires an open CORS policy. Since each tenant contains a dynamic list of domains, there's no way to know specifically which domains to whitelist at runtime without significant performance implications. This also means that the `serverURL` is not set, as this scopes all requests to a single domain.

Alternatively, if you know the domains of your tenants ahead of time and these values won't change often, you could simply remove the `domains` field altogether and instead use static values.

For more details on this, see the [CORS](https://payloadcms.com/docs/production/preventing-abuse#cross-origin-resource-sharing-cors) docs.

## Front-end

The frontend is scaffolded out in this example directory. You can view the code for rendering pages at `/src/app/(app)/[tenant]/[...slug]/page.tsx`. This is a starter template, you may need to adjust the app to better fit your needs.

## Questions

If you have any issues or questions, reach out to us on [Discord](https://discord.com/invite/payload) or start a [GitHub discussion](https://github.com/payloadcms/payload/discussions).
````

## File: tsconfig.json
````json
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
        "src/payload.config.ts"
      ],
      "@payload-types": [
        "src/payload-types.ts"
      ]
    },
    "target": "ES2022",
  },
  "include": [
    "next-env.d.ts",
    "**/*.ts",
    "**/*.tsx",
    ".next/types/**/*.ts",
  ],
  "exclude": [
    "node_modules"
  ]
}
````
