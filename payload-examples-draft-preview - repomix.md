This file is a merged representation of `payload/examples/draft-preview/` codebase (https://github.com/payloadcms/payload/tree/main/examples/draft-preview), combined into a single document by Repomix. The Payload Draft Preview Example demonstrates how to implement Draft Preview in Payload using Versions and Drafts. With Draft Preview, you can navigate to your front-end application and enter "draft mode", where your queries are modified to fetch draft content instead of published content. This is useful for seeing how your content will look before being published.

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
    (app)/
      [slug]/
        index.module.scss
        page.tsx
      exit-preview/
        route.ts
      preview/
        route.ts
      app.scss
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
  collections/
    Pages/
      access/
        loggedIn.ts
        publishedOrLoggedIn.ts
      hooks/
        formatSlug.ts
        revalidatePage.ts
      index.ts
    Users.ts
  components/
    AdminBar/
      index.module.scss
      index.tsx
    Button/
      index.module.scss
      index.tsx
    CMSLink/
      index.tsx
    Gutter/
      index.module.scss
      index.tsx
    Header/
      index.module.scss
      index.tsx
    RichText/
      index.module.scss
      index.tsx
      serialize.tsx
  fields/
    richText/
      elements.ts
      index.ts
      leaves.ts
    link.ts
  globals/
    MainMenu/
      hooks/
        revalidateMainMenu.ts
      index.ts
  migrations/
    seed.ts
  seed/
    home.ts
    page.ts
    pageDraft.ts
  utilities/
    deepMerge.ts
  payload-types.ts
  payload.config.ts
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

## File: src/app/(app)/[slug]/index.module.scss
````scss
.page {
  margin-top: calc(var(--base) * 2);
}
````

## File: src/app/(app)/[slug]/page.tsx
````typescript
import { draftMode } from 'next/headers'
import { notFound } from 'next/navigation'
import { getPayload } from 'payload'
import React, { cache, Fragment } from 'react'

import type { Page as PageType } from '@payload-types'

import { Gutter } from '../../../components/Gutter'
import RichText from '../../../components/RichText'
import config from '../../../payload.config'
import { home as homeStatic } from '../../../seed/home'
import classes from './index.module.scss'

export async function generateStaticParams() {
  const payload = await getPayload({ config })

  const pages = await payload.find({
    collection: 'pages',
    draft: false,
    limit: 1000,
    overrideAccess: false,
  })

  const params = pages.docs
    ?.filter((doc) => {
      return doc.slug !== 'home'
    })
    .map(({ slug }) => {
      return { slug }
    })

  return params || []
}

type Args = {
  params: Promise<{
    slug?: string
  }>
}

// eslint-disable-next-line no-restricted-exports
export default async function Page({ params: paramsPromise }: Args) {
  const { slug = 'home' } = await paramsPromise

  let page: null | PageType

  page = await queryPageBySlug({
    slug,
  })

  // Remove this code once your website is seeded
  if (!page && slug === 'home') {
    page = homeStatic
  }

  if (page === null) {
    return notFound()
  }

  return (
    <Fragment>
      <main className={classes.page}>
        <Gutter>
          <h1>{page?.title}</h1>
          <RichText content={page?.richText} />
        </Gutter>
      </main>
    </Fragment>
  )
}

const queryPageBySlug = cache(async ({ slug }: { slug: string }) => {
  const { isEnabled: draft } = await draftMode()

  const payload = await getPayload({ config })

  const result = await payload.find({
    collection: 'pages',
    draft,
    limit: 1,
    overrideAccess: draft,
    where: {
      slug: {
        equals: slug,
      },
    },
  })

  return result.docs?.[0] || null
})
````

## File: src/app/(app)/exit-preview/route.ts
````typescript
import { draftMode } from 'next/headers'

export async function GET(): Promise<Response> {
  const draft = await draftMode()
  draft.disable()
  return new Response('Draft mode is disabled')
}
````

## File: src/app/(app)/preview/route.ts
````typescript
import type { CollectionSlug, PayloadRequest } from 'payload'
import { getPayload } from 'payload'

import { draftMode } from 'next/headers'
import { redirect } from 'next/navigation'

import configPromise from '@payload-config'

export async function GET(
  req: {
    cookies: {
      get: (name: string) => {
        value: string
      }
    }
  } & Request,
): Promise<Response> {
  const payload = await getPayload({ config: configPromise })

  const { searchParams } = new URL(req.url)

  const path = searchParams.get('path')
  const collection = searchParams.get('collection') as CollectionSlug
  const slug = searchParams.get('slug')
  const previewSecret = searchParams.get('previewSecret')

  if (previewSecret !== process.env.PREVIEW_SECRET) {
    return new Response('You are not allowed to preview this page', { status: 403 })
  }

  if (!path || !collection || !slug) {
    return new Response('Insufficient search params', { status: 404 })
  }

  if (!path.startsWith('/')) {
    return new Response('This endpoint can only be used for relative previews', { status: 500 })
  }

  let user

  try {
    user = await payload.auth({
      req: req as unknown as PayloadRequest,
      headers: req.headers,
    })
  } catch (error) {
    payload.logger.error({ err: error }, 'Error verifying token for live preview')
    return new Response('You are not allowed to preview this page', { status: 403 })
  }

  const draft = await draftMode()

  if (!user) {
    draft.disable()
    return new Response('You are not allowed to preview this page', { status: 403 })
  }

  // You can add additional checks here to see if the user is allowed to preview this page

  draft.enable()

  redirect(path)
}
````

## File: src/app/(app)/app.scss
````scss
$breakpoint: 1000px;

:root {
  --max-width: 1600px;
  --foreground-rgb: 0, 0, 0;
  --background-rgb: 255, 255, 255;
  --block-spacing: 2rem;
  --gutter-h: 4rem;
  --base: 1rem;

  @media (max-width: $breakpoint) {
    --block-spacing: 1rem;
    --gutter-h: 2rem;
    --base: 0.75rem;
  }
}

@media (prefers-color-scheme: dark) {
  :root {
    --foreground-rgb: 255, 255, 255;
    --background-rgb: 7, 7, 7;
  }
}

* {
  box-sizing: border-box;
}

html {
  font-size: 20px;
  line-height: 1.5;
  font-family:
    system-ui,
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    Oxygen,
    Ubuntu,
    Cantarell,
    'Open Sans',
    'Helvetica Neue',
    sans-serif;

  @media (max-width: $breakpoint) {
    font-size: 16px;
  }
}

html,
body {
  max-width: 100vw;
  overflow-x: hidden;
}

body {
  margin: 0;
  color: rgb(var(--foreground-rgb));
  background: rgb(var(--background-rgb));
}

img {
  height: auto;
  max-width: 100%;
  display: block;
}

h1 {
  font-size: 4.5rem;
  line-height: 1.2;
  margin: 0 0 2.5rem 0;

  @media (max-width: $breakpoint) {
    font-size: 3rem;
    margin: 0 0 1.5rem 0;
  }
}

h2 {
  font-size: 3.5rem;
  line-height: 1.2;
  margin: 0 0 2.5rem 0;
}

h3 {
  font-size: 2.5rem;
  line-height: 1.2;
  margin: 0 0 2rem 0;
}

h4 {
  font-size: 1.5rem;
  line-height: 1.2;
  margin: 0 0 1rem 0;
}

h5 {
  font-size: 1.25rem;
  line-height: 1.2;
  margin: 0 0 1rem 0;
}

h6 {
  font-size: 1rem;
  line-height: 1.2;
  margin: 0 0 1rem 0;
}

a {
  color: inherit;
  text-decoration: none;
}

@media (prefers-color-scheme: dark) {
  html {
    color-scheme: dark;
  }
}
````

## File: src/app/(app)/layout.tsx
````typescript
import type { Metadata } from 'next'

import { draftMode } from 'next/headers'

import { AdminBar } from '../../components/AdminBar'
import { Header } from '../../components/Header'
import './app.scss'

export const metadata: Metadata = {
  description: 'Generated by create next app',
  title: 'Create Next App',
}

// eslint-disable-next-line no-restricted-exports
export default async function RootLayout({ children }: { children: React.ReactNode }) {
  const { isEnabled } = await draftMode()

  return (
    <html lang="en">
      <body>
        <AdminBar
          adminBarProps={{
            preview: isEnabled,
          }}
        />
        <Header />
        {children}
      </body>
    </html>
  )
}
````

## File: src/app/(app)/page.tsx
````typescript
import Page from './[slug]/page'

export default Page
````

## File: src/app/(payload)/admin/[[...segments]]/not-found.tsx
````typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import type { Metadata } from 'next'

import config from '@payload-config'
import { generatePageMetadata, NotFoundPage } from '@payloadcms/next/views'

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
  RootPage({ config, importMap, params, searchParams })

export default Page
````

## File: src/app/(payload)/admin/importMap.js
````javascript
import { RscEntrySlateCell as RscEntrySlateCell_0e78253914a550fdacd75626f1dabe17 } from '@payloadcms/richtext-slate/rsc'
import { RscEntrySlateField as RscEntrySlateField_0e78253914a550fdacd75626f1dabe17 } from '@payloadcms/richtext-slate/rsc'
import { BoldLeafButton as BoldLeafButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { BoldLeaf as BoldLeaf_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { CodeLeafButton as CodeLeafButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { CodeLeaf as CodeLeaf_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { ItalicLeafButton as ItalicLeafButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { ItalicLeaf as ItalicLeaf_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { StrikethroughLeafButton as StrikethroughLeafButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { StrikethroughLeaf as StrikethroughLeaf_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { UnderlineLeafButton as UnderlineLeafButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { UnderlineLeaf as UnderlineLeaf_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { BlockquoteElementButton as BlockquoteElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { BlockquoteElement as BlockquoteElement_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { H1ElementButton as H1ElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { Heading1Element as Heading1Element_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { H2ElementButton as H2ElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { Heading2Element as Heading2Element_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { H3ElementButton as H3ElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { Heading3Element as Heading3Element_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { H4ElementButton as H4ElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { Heading4Element as Heading4Element_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { H5ElementButton as H5ElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { Heading5Element as Heading5Element_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { H6ElementButton as H6ElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { Heading6Element as Heading6Element_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { IndentButton as IndentButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { IndentElement as IndentElement_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { ListItemElement as ListItemElement_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { LinkButton as LinkButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { LinkElement as LinkElement_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { WithLinks as WithLinks_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { OLElementButton as OLElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { OrderedListElement as OrderedListElement_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { RelationshipButton as RelationshipButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { RelationshipElement as RelationshipElement_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { WithRelationship as WithRelationship_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { TextAlignElementButton as TextAlignElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { ULElementButton as ULElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { UnorderedListElement as UnorderedListElement_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { UploadElementButton as UploadElementButton_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { UploadElement as UploadElement_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'
import { WithUpload as WithUpload_0b388c087d9de8c4f011dd323a130cfb } from '@payloadcms/richtext-slate/client'

export const importMap = {
  '@payloadcms/richtext-slate/rsc#RscEntrySlateCell':
    RscEntrySlateCell_0e78253914a550fdacd75626f1dabe17,
  '@payloadcms/richtext-slate/rsc#RscEntrySlateField':
    RscEntrySlateField_0e78253914a550fdacd75626f1dabe17,
  '@payloadcms/richtext-slate/client#BoldLeafButton':
    BoldLeafButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#BoldLeaf': BoldLeaf_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#CodeLeafButton':
    CodeLeafButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#CodeLeaf': CodeLeaf_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#ItalicLeafButton':
    ItalicLeafButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#ItalicLeaf': ItalicLeaf_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#StrikethroughLeafButton':
    StrikethroughLeafButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#StrikethroughLeaf':
    StrikethroughLeaf_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#UnderlineLeafButton':
    UnderlineLeafButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#UnderlineLeaf': UnderlineLeaf_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#BlockquoteElementButton':
    BlockquoteElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#BlockquoteElement':
    BlockquoteElement_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#H1ElementButton':
    H1ElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#Heading1Element':
    Heading1Element_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#H2ElementButton':
    H2ElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#Heading2Element':
    Heading2Element_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#H3ElementButton':
    H3ElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#Heading3Element':
    Heading3Element_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#H4ElementButton':
    H4ElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#Heading4Element':
    Heading4Element_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#H5ElementButton':
    H5ElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#Heading5Element':
    Heading5Element_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#H6ElementButton':
    H6ElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#Heading6Element':
    Heading6Element_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#IndentButton': IndentButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#IndentElement': IndentElement_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#ListItemElement':
    ListItemElement_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#LinkButton': LinkButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#LinkElement': LinkElement_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#WithLinks': WithLinks_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#OLElementButton':
    OLElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#OrderedListElement':
    OrderedListElement_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#RelationshipButton':
    RelationshipButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#RelationshipElement':
    RelationshipElement_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#WithRelationship':
    WithRelationship_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#TextAlignElementButton':
    TextAlignElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#ULElementButton':
    ULElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#UnorderedListElement':
    UnorderedListElement_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#UploadElementButton':
    UploadElementButton_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#UploadElement': UploadElement_0b388c087d9de8c4f011dd323a130cfb,
  '@payloadcms/richtext-slate/client#WithUpload': WithUpload_0b388c087d9de8c4f011dd323a130cfb,
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
import type { ServerFunctionClient } from 'payload'

import '@payloadcms/next/css'
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import config from '@payload-config'
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

## File: src/collections/Pages/access/loggedIn.ts
````typescript
import type { Access } from 'payload'

export const loggedIn: Access = ({ req: { user } }) => {
  return Boolean(user)
}
````

## File: src/collections/Pages/access/publishedOrLoggedIn.ts
````typescript
import type { Access } from 'payload'

export const publishedOrLoggedIn: Access = ({ req: { user } }) => {
  if (user) {
    return true
  }

  return {
    or: [
      {
        _status: {
          equals: 'published',
        },
      },
    ],
  }
}
````

## File: src/collections/Pages/hooks/formatSlug.ts
````typescript
import type { FieldHook } from 'payload'

const format = (val: string): string =>
  val
    .replace(/ /g, '-')
    .replace(/[^\w-]+/g, '')
    .toLowerCase()

export const formatSlug =
  (fallback: string): FieldHook =>
  ({ data, operation, originalDoc, value }) => {
    if (typeof value === 'string') {
      return format(value)
    }

    if (operation === 'create') {
      const fallbackData = data?.[fallback] || originalDoc?.[fallback]

      if (fallbackData && typeof fallbackData === 'string') {
        return format(fallbackData)
      }
    }

    return value
  }
````

## File: src/collections/Pages/hooks/revalidatePage.ts
````typescript
import type { CollectionAfterChangeHook } from 'payload'

import { revalidatePath } from 'next/cache'

import type { Page } from '@payload-types'

export const revalidatePage: CollectionAfterChangeHook<Page> = ({ doc, previousDoc, req }) => {
  if (req.context.skipRevalidate) {
    return doc
  }

  if (doc._status === 'published') {
    const path = doc.slug === 'home' ? '/' : `/${doc.slug}`
    req.payload.logger.info(`Revalidating page at path: ${path}`)
    revalidatePath(path)
  }

  if (previousDoc?._status === 'published' && doc._status !== 'published') {
    const oldPath = previousDoc.slug === 'home' ? '/' : `/${previousDoc.slug}`
    req.payload.logger.info(`Revalidating old page at path: ${oldPath}`)
    revalidatePath(oldPath)
  }

  return doc
}
````

## File: src/collections/Pages/index.ts
````typescript
import type { CollectionConfig, CollectionSlug } from 'payload'

import richText from '../../fields/richText'
import { loggedIn } from './access/loggedIn'
import { publishedOrLoggedIn } from './access/publishedOrLoggedIn'
import { formatSlug } from './hooks/formatSlug'
import { revalidatePage } from './hooks/revalidatePage'

export const Pages: CollectionConfig = {
  slug: 'pages',
  access: {
    create: loggedIn,
    delete: loggedIn,
    read: publishedOrLoggedIn,
    update: loggedIn,
  },
  admin: {
    defaultColumns: ['title', 'slug', 'updatedAt'],
    preview: ({ slug, collection }: { slug: string; collection: CollectionSlug }) => {
      const encodedParams = new URLSearchParams({
        slug,
        collection,
        path: `/${slug}`,
        previewSecret: process.env.PREVIEW_SECRET || '',
      })

      return `${process.env.NEXT_PUBLIC_SERVER_URL}/preview?${encodedParams.toString()}`
    },
    useAsTitle: 'title',
  },
  fields: [
    {
      name: 'title',
      type: 'text',
      required: true,
    },
    {
      name: 'slug',
      type: 'text',
      admin: {
        position: 'sidebar',
      },
      hooks: {
        beforeValidate: [formatSlug('title')],
      },
      index: true,
      label: 'Slug',
    },
    richText(),
  ],
  hooks: {
    afterChange: [revalidatePage],
  },
  versions: {
    drafts: true,
  },
}
````

## File: src/collections/Users.ts
````typescript
import type { CollectionConfig } from 'payload'

export const Users: CollectionConfig = {
  slug: 'users',
  admin: {
    useAsTitle: 'email',
  },
  auth: true,
  fields: [],
}
````

## File: src/components/AdminBar/index.module.scss
````scss
.adminBar {
  z-index: 10;
  width: 100%;
  background-color: rgba(var(--foreground-rgb), 0.075);
  padding: calc(var(--base) * 0.5) 0;
  display: none;
  visibility: hidden;
  opacity: 0;
  transition: opacity 150ms linear;
}

.payloadAdminBar {
  color: rgb(var(--foreground-rgb)) !important;
}

.show {
  display: block;
  visibility: visible;
  opacity: 1;
}

.controls {
  & > *:not(:last-child) {
    margin-right: calc(var(--base) * 0.5) !important;
  }
}

.user {
  margin-right: calc(var(--base) * 0.5) !important;
}

.logo {
  margin-right: calc(var(--base) * 0.5) !important;
}

.innerLogo {
  width: 100%;
}

.container {
  position: relative;
}

.hr {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  background-color: rbg(var(--background-rgb));
  height: 2px;
}
````

## File: src/components/AdminBar/index.tsx
````typescript
'use client'

import type { PayloadAdminBarProps } from '@payloadcms/admin-bar'

import { useRouter } from 'next/navigation'
import { PayloadAdminBar } from '@payloadcms/admin-bar'
import React, { useState } from 'react'

import { Gutter } from '../Gutter'
import classes from './index.module.scss'

const collectionLabels = {
  pages: {
    plural: 'Pages',
    singular: 'Page',
  },
}

const Title: React.FC = () => <span>Dashboard</span>

export const AdminBar: React.FC<{
  adminBarProps?: PayloadAdminBarProps
}> = (props) => {
  const { adminBarProps } = props || {}
  const [show, setShow] = useState(false)
  const collection = 'pages'
  const router = useRouter()

  const onAuthChange = React.useCallback((user) => {
    setShow(user?.id)
  }, [])

  return (
    <div className={[classes.adminBar, show && classes.show].filter(Boolean).join(' ')}>
      <Gutter className={classes.container}>
        <PayloadAdminBar
          {...adminBarProps}
          className={classes.payloadAdminBar}
          classNames={{
            controls: classes.controls,
            logo: classes.logo,
            user: classes.user,
          }}
          cmsURL={process.env.NEXT_PUBLIC_SERVER_URL}
          collectionSlug={collection}
          collectionLabels={{
            plural: collectionLabels[collection]?.plural || 'Pages',
            singular: collectionLabels[collection]?.singular || 'Page',
          }}
          logo={<Title />}
          onAuthChange={onAuthChange}
          onPreviewExit={() => {
            fetch('/next/exit-preview')
              .then(() => {
                router.push('/')
                router.refresh()
              })
              .catch((error) => {
                console.error('Error exiting preview:', error)
              })
          }}
          style={{
            backgroundColor: 'transparent',
            padding: 0,
            position: 'relative',
            zIndex: 'unset',
          }}
        />
      </Gutter>
    </div>
  )
}
````

## File: src/components/Button/index.module.scss
````scss
.button {
  border: none;
  cursor: pointer;
  display: inline-flex;
  justify-content: center;
  background-color: transparent;
}

.content {
  display: flex;
  align-items: center;
  justify-content: space-around;

  svg {
    margin-right: calc(var(--base) / 2);
    width: var(--base);
    height: var(--base);
  }
}

.label {
  text-align: center;
  display: flex;
  align-items: center;
}

.button {
  text-decoration: none;
  display: inline-flex;
  padding: 12px 24px;
}

.primary--white {
  background-color: black;
  color: white;
}

.primary--black {
  background-color: white;
  color: black;
}

.secondary--white {
  background-color: white;
  box-shadow: inset 0 0 0 1px black;
}

.secondary--black {
  background-color: black;
  box-shadow: inset 0 0 0 1px white;
}

.appearance--default {
  padding: 0;
}
````

## File: src/components/Button/index.tsx
````typescript
import type { ElementType } from 'react'

import Link from 'next/link'
import React from 'react'

import classes from './index.module.scss'

export type Props = {
  appearance?: 'default' | 'primary' | 'secondary'
  className?: string
  disabled?: boolean
  el?: 'a' | 'button' | 'link'
  href?: string
  label?: string
  newTab?: boolean | null
  onClick?: () => void
  type?: 'button' | 'submit'
}

export const Button: React.FC<Props> = ({
  type = 'button',
  appearance,
  className: classNameFromProps,
  disabled,
  el: elFromProps = 'link',
  href,
  label,
  newTab,
  onClick,
}) => {
  let el = elFromProps
  const newTabProps = newTab ? { rel: 'noopener noreferrer', target: '_blank' } : {}
  const className = [
    classes.button,
    classNameFromProps,
    classes[`appearance--${appearance}`],
    classes.button,
  ]
    .filter(Boolean)
    .join(' ')

  const content = (
    <div className={classes.content}>
      {/* <Chevron /> */}
      <span className={classes.label}>{label}</span>
    </div>
  )

  if (onClick || type === 'submit') {
    el = 'button'
  }

  if (el === 'link') {
    return (
      <Link className={className} href={href || ''} {...newTabProps} onClick={onClick}>
        {content}
      </Link>
    )
  }

  const Element: ElementType = el

  return (
    <Element
      className={className}
      href={href}
      type={type}
      {...newTabProps}
      disabled={disabled}
      onClick={onClick}
    >
      {content}
    </Element>
  )
}
````

## File: src/components/CMSLink/index.tsx
````typescript
import Link from 'next/link'
import React from 'react'

import type { Page } from '@payload-types'

import { Button } from '../Button'

export type CMSLinkType = {
  appearance?: 'default' | 'primary' | 'secondary'
  children?: React.ReactNode
  className?: string
  label?: string
  newTab?: boolean | null
  reference?: {
    relationTo: 'pages'
    value: number | Page | string
  } | null
  type?: 'custom' | 'reference' | null
  url?: null | string
}

export const CMSLink: React.FC<CMSLinkType> = ({
  type,
  appearance,
  children,
  className,
  label,
  newTab,
  reference,
  url,
}) => {
  const href =
    type === 'reference' && typeof reference?.value === 'object' && reference.value.slug
      ? `${reference?.relationTo !== 'pages' ? `/${reference?.relationTo}` : ''}/${
          reference.value.slug
        }`
      : url

  if (!href) {
    return null
  }

  if (!appearance) {
    const newTabProps = newTab ? { rel: 'noopener noreferrer', target: '_blank' } : {}

    if (type === 'custom') {
      return (
        <a href={url || ''} {...newTabProps} className={className}>
          {label && label}
          {children ? <>{children}</> : null}
        </a>
      )
    }

    if (href) {
      return (
        <Link href={href} {...newTabProps} className={className} prefetch={false}>
          {label && label}
          {children ? <>{children}</> : null}
        </Link>
      )
    }
  }

  const buttonProps = {
    appearance,
    href,
    label,
    newTab,
  }

  return <Button className={className} {...buttonProps} el="link" />
}
````

## File: src/components/Gutter/index.module.scss
````scss
.gutter {
  max-width: var(--max-width);
  width: 100%;
  margin: auto;
}

.gutterLeft {
  padding-left: var(--gutter-h);
}

.gutterRight {
  padding-right: var(--gutter-h);
}
````

## File: src/components/Gutter/index.tsx
````typescript
import React from 'react'

import classes from './index.module.scss'

type Props = {
  children: React.ReactNode
  className?: string
  left?: boolean
  ref?: React.Ref<HTMLDivElement>
  right?: boolean
}

export const Gutter: React.FC<Props & { ref?: React.Ref<HTMLDivElement> }> = (props) => {
  const { children, className, left = true, right = true, ref } = props

  return (
    <div
      className={[
        classes.gutter,
        left && classes.gutterLeft,
        right && classes.gutterRight,
        className,
      ]
        .filter(Boolean)
        .join(' ')}
      ref={ref}
    >
      {children}
    </div>
  )
}
````

## File: src/components/Header/index.module.scss
````scss
.header {
  padding: var(--base) 0;
}

.wrap {
  display: flex;
  justify-content: space-between;
  gap: calc(var(--base) / 2);
  flex-wrap: wrap;
}

.logo {
  flex-shrink: 0;
}

.nav {
  display: flex;
  align-items: center;
  gap: var(--base);
  white-space: nowrap;
  overflow: hidden;
  flex-wrap: wrap;

  a {
    display: block;
    text-decoration: none;
  }

  @media (max-width: 1000px) {
    gap: 0 calc(var(--base) / 2);
  }
}
````

## File: src/components/Header/index.tsx
````typescript
import Image from 'next/image'
import Link from 'next/link'
import React from 'react'
import { getPayload } from 'payload'
import configPromise from '@payload-config'

import type { MainMenu } from '@payload-types'

import { CMSLink } from '../CMSLink'
import { Gutter } from '../Gutter'
import classes from './index.module.scss'

export async function Header() {
  const payload = await getPayload({ config: configPromise })

  const header: MainMenu = await payload.findGlobal({
    slug: 'main-menu',
    depth: 1,
  })

  const navItems = header?.navItems || []

  return (
    <header className={classes.header}>
      <Gutter className={classes.wrap}>
        <Link className={classes.logo} href="/">
          <picture>
            <source
              media="(prefers-color-scheme: dark)"
              srcSet="https://raw.githubusercontent.com/payloadcms/payload/main/packages/ui/src/assets/payload-logo-light.svg"
            />
            <Image
              alt="Payload Logo"
              height={30}
              src="https://raw.githubusercontent.com/payloadcms/payload/main/packages/ui/src/assets/payload-logo-dark.svg"
              width={150}
            />
          </picture>
        </Link>
        <nav className={classes.nav}>
          {navItems.map(({ link }, i) => {
            const sanitizedLink = {
              ...link,
              type: link.type ?? undefined,
              newTab: link.newTab ?? false,
              url: link.url ?? undefined,
            }

            return <CMSLink key={i} {...sanitizedLink} />
          })}
        </nav>
      </Gutter>
    </header>
  )
}

export default Header
````

## File: src/components/RichText/index.module.scss
````scss
.richText {
  :first-child {
    margin-top: 0;
  }

  a {
    text-decoration: underline;
  }
}
````

## File: src/components/RichText/index.tsx
````typescript
import React from 'react'

import classes from './index.module.scss'
import serialize from './serialize'

const RichText: React.FC<{ className?: string; content: any }> = ({ className, content }) => {
  if (!content) {
    return null
  }

  return (
    <div className={[classes.richText, className].filter(Boolean).join(' ')}>
      {serialize(content)}
    </div>
  )
}

export default RichText
````

## File: src/components/RichText/serialize.tsx
````typescript
import escapeHTML from 'escape-html'
import React, { Fragment } from 'react'
import { Text } from 'slate'

type Children = Leaf[]

type Leaf = {
  [key: string]: unknown
  children: Children
  type: string
  url?: string
  value?: {
    alt: string
    url: string
  }
}

const serialize = (children: Children): React.ReactNode[] =>
  children.map((node, i) => {
    if (Text.isText(node)) {
      let text = <span dangerouslySetInnerHTML={{ __html: escapeHTML(node.text) }} />

      if (node.bold) {
        text = <strong key={i}>{text}</strong>
      }

      if (node.code) {
        text = <code key={i}>{text}</code>
      }

      if (node.italic) {
        text = <em key={i}>{text}</em>
      }

      if (node.underline) {
        text = (
          <span key={i} style={{ textDecoration: 'underline' }}>
            {text}
          </span>
        )
      }

      if (node.strikethrough) {
        text = (
          <span key={i} style={{ textDecoration: 'line-through' }}>
            {text}
          </span>
        )
      }

      return <Fragment key={i}>{text}</Fragment>
    }

    if (!node) {
      return null
    }

    switch (node.type) {
      case 'blockquote':
        return <blockquote key={i}>{serialize(node.children)}</blockquote>
      case 'h1':
        return <h1 key={i}>{serialize(node.children)}</h1>
      case 'h2':
        return <h2 key={i}>{serialize(node.children)}</h2>
      case 'h3':
        return <h3 key={i}>{serialize(node.children)}</h3>
      case 'h4':
        return <h4 key={i}>{serialize(node.children)}</h4>
      case 'h5':
        return <h5 key={i}>{serialize(node.children)}</h5>
      case 'h6':
        return <h6 key={i}>{serialize(node.children)}</h6>
      case 'li':
        return <li key={i}>{serialize(node.children)}</li>
      case 'link':
        return (
          <a href={escapeHTML(node.url)} key={i}>
            {serialize(node.children)}
          </a>
        )
      case 'ol':
        return <ol key={i}>{serialize(node.children)}</ol>
      case 'ul':
        return <ul key={i}>{serialize(node.children)}</ul>

      default:
        return <p key={i}>{serialize(node.children)}</p>
    }
  })

export default serialize
````

## File: src/fields/richText/elements.ts
````typescript
import type { RichTextElement } from '@payloadcms/richtext-slate'

const elements: RichTextElement[] = ['h1', 'h2', 'h3', 'h4', 'h5', 'h6', 'blockquote', 'link']

export default elements
````

## File: src/fields/richText/index.ts
````typescript
import type { RichTextElement, RichTextLeaf } from '@payloadcms/richtext-slate'
import type { RichTextField } from 'payload'

import { slateEditor } from '@payloadcms/richtext-slate'

import deepMerge from '../../utilities/deepMerge'
import link from '../link'
import elements from './elements'
import leaves from './leaves'

type RichText = (
  overrides?: Partial<RichTextField>,
  additions?: {
    elements?: RichTextElement[]
    leaves?: RichTextLeaf[]
  },
) => RichTextField

const richText: RichText = (
  overrides = {},
  additions = {
    elements: [],
    leaves: [],
  },
) =>
  deepMerge<RichTextField, Partial<RichTextField>>(
    {
      name: 'richText',
      type: 'richText',
      editor: slateEditor({
        admin: {
          elements: [...elements, ...(additions.elements || [])],
          leaves: [...leaves, ...(additions.leaves || [])],
          upload: {
            collections: {
              media: {
                fields: [
                  {
                    name: 'caption',
                    type: 'richText',
                    editor: slateEditor({
                      admin: {
                        elements: [...elements],
                        leaves: [...leaves],
                      },
                    }),
                    label: 'Caption',
                  },
                  {
                    name: 'alignment',
                    type: 'radio',
                    label: 'Alignment',
                    options: [
                      {
                        label: 'Left',
                        value: 'left',
                      },
                      {
                        label: 'Center',
                        value: 'center',
                      },
                      {
                        label: 'Right',
                        value: 'right',
                      },
                    ],
                  },
                  {
                    name: 'enableLink',
                    type: 'checkbox',
                    label: 'Enable Link',
                  },
                  link({
                    appearances: false,
                    disableLabel: true,
                    overrides: {
                      admin: {
                        condition: (_: any, data: { enableLink: any }) => Boolean(data?.enableLink),
                      },
                    },
                  }),
                ],
              },
            },
          },
        },
      }),
      required: true,
    },
    overrides,
  )

export default richText
````

## File: src/fields/richText/leaves.ts
````typescript
import type { RichTextLeaf } from '@payloadcms/richtext-slate'

const defaultLeaves: RichTextLeaf[] = ['bold', 'italic', 'underline']

export default defaultLeaves
````

## File: src/fields/link.ts
````typescript
import type { Field } from 'payload'

import deepMerge from '../utilities/deepMerge'

export const appearanceOptions = {
  default: {
    label: 'Default',
    value: 'default',
  },
  primary: {
    label: 'Primary Button',
    value: 'primary',
  },
  secondary: {
    label: 'Secondary Button',
    value: 'secondary',
  },
}

export type LinkAppearances = 'default' | 'primary' | 'secondary'

type LinkType = (options?: {
  appearances?: false | LinkAppearances[]
  disableLabel?: boolean
  overrides?: Record<string, unknown>
}) => Field

const link: LinkType = ({ appearances, disableLabel = false, overrides = {} } = {}) => {
  const linkResult: Field = {
    name: 'link',
    type: 'group',
    admin: {
      hideGutter: true,
    },
    fields: [
      {
        type: 'row',
        fields: [
          {
            name: 'type',
            type: 'radio',
            admin: {
              layout: 'horizontal',
              width: '50%',
            },
            defaultValue: 'reference',
            options: [
              {
                label: 'Internal link',
                value: 'reference',
              },
              {
                label: 'Custom URL',
                value: 'custom',
              },
            ],
          },
          {
            name: 'newTab',
            type: 'checkbox',
            admin: {
              style: {
                alignSelf: 'flex-end',
              },
              width: '50%',
            },
            label: 'Open in new tab',
          },
        ],
      },
    ],
  }

  const linkTypes: Field[] = [
    {
      name: 'reference',
      type: 'relationship',
      admin: {
        condition: (_, siblingData) => siblingData?.type === 'reference',
      },
      label: 'Document to link to',
      maxDepth: 1,
      relationTo: ['pages'],
      required: true,
    },
    {
      name: 'url',
      type: 'text',
      admin: {
        condition: (_, siblingData) => siblingData?.type === 'custom',
      },
      label: 'Custom URL',
      required: true,
    },
  ]

  if (!disableLabel) {
    if (linkTypes[0].admin) {
      linkTypes[0].admin.width = '50%'
    }
    if (linkTypes[1].admin) {
      linkTypes[1].admin.width = '50%'
    }

    linkResult.fields.push({
      type: 'row',
      fields: [
        ...linkTypes,
        {
          name: 'label',
          type: 'text',
          admin: {
            width: '50%',
          },
          label: 'Label',
          required: true,
        },
      ],
    })
  } else {
    linkResult.fields = [...linkResult.fields, ...linkTypes]
  }

  if (appearances !== false) {
    let appearanceOptionsToUse = [
      appearanceOptions.default,
      appearanceOptions.primary,
      appearanceOptions.secondary,
    ]

    if (appearances) {
      appearanceOptionsToUse = appearances.map((appearance) => appearanceOptions[appearance])
    }

    linkResult.fields.push({
      name: 'appearance',
      type: 'select',
      admin: {
        description: 'Choose how the link should be rendered.',
      },
      defaultValue: 'default',
      options: appearanceOptionsToUse,
    })
  }

  return deepMerge(linkResult, overrides)
}

export default link
````

## File: src/globals/MainMenu/hooks/revalidateMainMenu.ts
````typescript
import type { GlobalAfterChangeHook } from 'payload'

import { revalidateTag } from 'next/cache'

export const revalidateMainMenu: GlobalAfterChangeHook = ({ doc, req, req: { payload } }) => {
  payload.logger.info(`Revalidating main menu`)

  if (req.context.skipRevalidate) {
    return doc
  }

  revalidateTag('global_main-menu')

  return doc
}
````

## File: src/globals/MainMenu/index.ts
````typescript
import type { GlobalConfig } from 'payload'

import link from '../../fields/link'
import { revalidateMainMenu } from './hooks/revalidateMainMenu'

export const MainMenu: GlobalConfig = {
  slug: 'main-menu',
  access: {
    read: () => true,
  },
  fields: [
    {
      name: 'navItems',
      type: 'array',
      fields: [
        link({
          appearances: false,
        }),
      ],
      maxRows: 6,
    },
  ],
  hooks: {
    afterChange: [revalidateMainMenu],
  },
}
````

## File: src/migrations/seed.ts
````typescript
import type { MigrateUpArgs } from '@payloadcms/db-mongodb'

import { home } from '../seed/home'
import { examplePage } from '../seed/page'
import { examplePageDraft } from '../seed/pageDraft'

export async function up({ payload }: MigrateUpArgs): Promise<void> {
  await payload.create({
    collection: 'users',
    data: {
      email: 'demo@payloadcms.com',
      password: 'demo',
    },
  })

  const { id: examplePageID } = await payload.create({
    collection: 'pages',
    context: {
      skipRevalidate: true,
    },
    data: examplePage as any, // eslint-disable-line
  })

  await payload.update({
    id: examplePageID,
    collection: 'pages',
    context: {
      skipRevalidate: true,
    },
    data: examplePageDraft as any, // eslint-disable-line
    draft: true,
  })

  const homepageJSON = JSON.parse(JSON.stringify(home).replace('{{DRAFT_PAGE_ID}}', examplePageID))

  const { id: homePageID } = await payload.create({
    collection: 'pages',
    context: {
      skipRevalidate: true,
    },
    data: homepageJSON,
  })

  await payload.updateGlobal({
    slug: 'main-menu',
    context: {
      skipRevalidate: true,
    },
    data: {
      navItems: [
        {
          link: {
            type: 'reference',
            label: 'Home',
            reference: {
              relationTo: 'pages',
              value: homePageID,
            },
            url: '',
          },
        },
        {
          link: {
            type: 'reference',
            label: 'Example Page',
            reference: {
              relationTo: 'pages',
              value: examplePageID,
            },
            url: '',
          },
        },
        {
          link: {
            type: 'custom',
            label: 'Dashboard',
            reference: undefined,
            url: 'http://localhost:3000/admin',
          },
        },
      ],
    },
  })
}
````

## File: src/seed/home.ts
````typescript
import type { Page } from '@payload-types'

// Used for pre-seeded content so that the homepage is not empty
// @ts-expect-error: Page type is not fully compatible with the provided object structure
export const home: Page = {
  slug: 'home',
  _status: 'published',
  richText: [
    {
      children: [
        { text: 'This is a ' },
        { type: 'link', children: [{ text: '' }], newTab: true, url: 'https://nextjs.org/' },
        { text: '' },
        {
          type: 'link',
          children: [{ text: 'Next.js' }],
          linkType: 'custom',
          newTab: true,
          url: 'https://nextjs.org/',
        },
        { text: " app made explicitly for Payload's " },
        {
          type: 'link',
          children: [{ text: 'Draft Preview Example' }],
          linkType: 'custom',
          newTab: true,
          url: 'https://github.com/payloadcms/payload/tree/main/examples/draft-preview/payload',
        },
        { text: '. This example demonstrates how to implement draft preview into Payload using ' },
        {
          type: 'link',
          children: [{ text: 'Drafts' }],
          newTab: true,
          url: 'https://payloadcms.com/docs/versions/drafts#drafts',
        },
        { text: '.' },
      ],
    },
    { children: [{ text: '' }] },
    {
      children: [
        {
          type: 'link',
          children: [{ text: 'Log in to the admin panel' }],
          linkType: 'custom',
          newTab: true,
          url: 'http://localhost:3000/admin',
        },
        { text: ' and refresh this page to see the ' },
        {
          type: 'link',
          children: [{ text: 'Payload Admin Bar' }],
          linkType: 'custom',
          newTab: true,
          url: 'https://github.com/payloadcms/payload/tree/main/packages/admin-bar',
        },
        {
          text: ' appear at the top of this site. This will allow you to seamlessly navigate between the two apps. Then, navigate to the ',
        },
        {
          type: 'link',
          children: [{ text: 'example page' }],
          linkType: 'custom',
          url: 'http://localhost:3000/example-page',
        },
        { text: ' to see how we control access to draft content. ' },
      ],
    },
  ],
  title: 'Home Page',
}
````

## File: src/seed/page.ts
````typescript
import type { Page } from '@payload-types'

export const examplePage: Partial<Page> = {
  slug: 'example-page',
  _status: 'published',
  richText: [
    {
      children: [
        {
          text: 'This is an example page with two versions, draft and published. You are currently seeing ',
        },
        {
          bold: true,
          text: 'published',
        },
        {
          text: ' content because you are not in preview mode. ',
        },
        {
          type: 'link',
          children: [{ text: 'Log in to the admin panel' }],
          linkType: 'custom',
          newTab: true,
          url: 'http://localhost:3000/admin',
        },
        {
          text: ' and click "preview" to return to this page and view the latest draft content in Next.js preview mode. To make additional changes to the draft, click "save draft" before returning to the preview.',
        },
      ],
    },
  ],
  title: 'Example Page (Published)',
}
````

## File: src/seed/pageDraft.ts
````typescript
import type { Page } from '@payload-types'

export const examplePageDraft: Partial<Page> = {
  richText: [
    {
      children: [
        {
          text: 'This page is an example page with two versions, draft and published. You are currently seeing ',
        },
        {
          bold: true,
          text: 'draft',
        },
        {
          text: ' content because you in preview mode. ',
        },
        {
          type: 'link',
          children: [{ text: 'Log out' }],
          linkType: 'custom',
          newTab: true,
          url: 'http://localhost:3000/admin/logout',
        },
        {
          text: ' or click "exit preview mode" from the Payload Admin Bar to see the latest published content. To make additional changes to the draft, click "save draft" before returning to the preview.',
        },
      ],
    },
  ],
  title: 'Example Page (Draft)',
}
````

## File: src/utilities/deepMerge.ts
````typescript
// @ts-nocheck

/**
 * Simple object check.
 * @param item
 * @returns {boolean}
 */
export function isObject(item: unknown): boolean {
  return item && typeof item === 'object' && !Array.isArray(item)
}

/**
 * Deep merge two objects.
 * @param target
 * @param ...sources
 */
// eslint-disable-next-line no-restricted-exports
export default function deepMerge<T, R>(target: T, source: R): T {
  const output = { ...target }
  if (isObject(target) && isObject(source)) {
    Object.keys(source).forEach((key) => {
      if (isObject(source[key])) {
        if (!(key in target)) {
          Object.assign(output, { [key]: source[key] })
        } else {
          output[key] = deepMerge(target[key], source[key])
        }
      } else {
        Object.assign(output, { [key]: source[key] })
      }
    })
  }

  return output
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

export interface Config {
  auth: {
    users: UserAuthOperations;
  };
  collections: {
    pages: Page;
    users: User;
    'payload-locked-documents': PayloadLockedDocument;
    'payload-preferences': PayloadPreference;
    'payload-migrations': PayloadMigration;
  };
  collectionsJoins: {};
  collectionsSelect: {
    pages: PagesSelect<false> | PagesSelect<true>;
    users: UsersSelect<false> | UsersSelect<true>;
    'payload-locked-documents': PayloadLockedDocumentsSelect<false> | PayloadLockedDocumentsSelect<true>;
    'payload-preferences': PayloadPreferencesSelect<false> | PayloadPreferencesSelect<true>;
    'payload-migrations': PayloadMigrationsSelect<false> | PayloadMigrationsSelect<true>;
  };
  db: {
    defaultIDType: string;
  };
  globals: {
    'main-menu': MainMenu;
  };
  globalsSelect: {
    'main-menu': MainMenuSelect<false> | MainMenuSelect<true>;
  };
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
  id: string;
  title: string;
  slug?: string | null;
  richText: {
    [k: string]: unknown;
  }[];
  updatedAt: string;
  createdAt: string;
  _status?: ('draft' | 'published') | null;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "users".
 */
export interface User {
  id: string;
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
  id: string;
  document?:
    | ({
        relationTo: 'pages';
        value: string | Page;
      } | null)
    | ({
        relationTo: 'users';
        value: string | User;
      } | null);
  globalSlug?: string | null;
  user: {
    relationTo: 'users';
    value: string | User;
  };
  updatedAt: string;
  createdAt: string;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "payload-preferences".
 */
export interface PayloadPreference {
  id: string;
  user: {
    relationTo: 'users';
    value: string | User;
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
  id: string;
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
  title?: T;
  slug?: T;
  richText?: T;
  updatedAt?: T;
  createdAt?: T;
  _status?: T;
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
 * via the `definition` "main-menu".
 */
export interface MainMenu {
  id: string;
  navItems?:
    | {
        link: {
          type?: ('reference' | 'custom') | null;
          newTab?: boolean | null;
          reference?: {
            relationTo: 'pages';
            value: string | Page;
          } | null;
          url?: string | null;
          label: string;
        };
        id?: string | null;
      }[]
    | null;
  updatedAt?: string | null;
  createdAt?: string | null;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "main-menu_select".
 */
export interface MainMenuSelect<T extends boolean = true> {
  navItems?:
    | T
    | {
        link?:
          | T
          | {
              type?: T;
              newTab?: T;
              reference?: T;
              url?: T;
              label?: T;
            };
        id?: T;
      };
  updatedAt?: T;
  createdAt?: T;
  globalType?: T;
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
import { slateEditor } from '@payloadcms/richtext-slate'
import { fileURLToPath } from 'node:url'
import path from 'path'
import { buildConfig } from 'payload'

import { Pages } from './collections/Pages'
import { Users } from './collections/Users'
import { MainMenu } from './globals/MainMenu'

const filename = fileURLToPath(import.meta.url)
const dirname = path.dirname(filename)

// eslint-disable-next-line no-restricted-exports
export default buildConfig({
  admin: {
    importMap: {
      baseDir: path.resolve(dirname),
    },
  },
  collections: [Pages, Users],
  cors: [process.env.NEXT_PUBLIC_SERVER_URL || ''].filter(Boolean),
  csrf: [process.env.NEXT_PUBLIC_SERVER_URL || ''].filter(Boolean),
  db: mongooseAdapter({
    url: process.env.DATABASE_URI || '',
  }),
  editor: slateEditor({}),
  globals: [MainMenu],
  graphQL: {
    schemaOutputFile: path.resolve(dirname, 'generated-schema.graphql'),
  },
  secret: process.env.PAYLOAD_SECRET || '',
  typescript: {
    outputFile: path.resolve(dirname, 'payload-types.ts'),
  },
})
````

## File: .env.example
````
# Database connection string
DATABASE_URI=mongodb://127.0.0.1/payload-draft-preview-example

# Used to encrypt JWT tokens
PAYLOAD_SECRET=YOUR_SECRET_HERE

# Used to configure CORS, format links and more. No trailing slash
NEXT_PUBLIC_SERVER_URL=http://localhost:3000

# Used to validate the preview request
PREVIEW_SECRET=YOUR_SECRET_HERE
````

## File: .eslintrc.cjs
````
module.exports = {
  extends: 'next',
  root: true,
  parserOptions: {
    project: ['./tsconfig.json'],
    tsconfigRootDir: __dirname,
  },
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
}

export default withPayload(nextConfig)
````

## File: package.json
````json
{
  "name": "payload-example-preview",
  "version": "1.0.0",
  "description": "Payload preview example.",
  "license": "MIT",
  "type": "module",
  "main": "dist/server.js",
  "scripts": {
    "build": "cross-env NODE_OPTIONS=--no-deprecation next build",
    "dev": "cross-env NODE_OPTIONS=--no-deprecation && pnpm seed && next dev",
    "generate:importmap": "cross-env NODE_OPTIONS=--no-deprecation payload generate:importmap",
    "generate:schema": "payload-graphql generate:schema",
    "generate:types": "cross-env NODE_OPTIONS=--no-deprecation payload generate:types",
    "payload": "cross-env NODE_OPTIONS=--no-deprecation payload",
    "seed": "npm run payload migrate:fresh",
    "start": "cross-env NODE_OPTIONS=--no-deprecation next start"
  },
  "dependencies": {
    "@payloadcms/admin-bar": "latest",
    "@payloadcms/db-mongodb": "latest",
    "@payloadcms/next": "latest",
    "@payloadcms/richtext-slate": "latest",
    "@payloadcms/ui": "latest",
    "dotenv": "^8.2.0",
    "escape-html": "^1.0.3",
    "graphql": "^16.9.0",
    "next": "^15.0.0",
    "payload": "latest",
    "react": "19.0.0",
    "react-dom": "19.0.0"
  },
  "devDependencies": {
    "@payloadcms/graphql": "latest",
    "@swc/core": "^1.6.13",
    "@types/escape-html": "^1.0.2",
    "@types/react": "19.0.1",
    "@types/react-dom": "19.0.1",
    "eslint": "^8.57.0",
    "eslint-config-next": "^15.0.0",
    "slate": "^0.82.0",
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
# Payload Draft Preview Example

The [Payload Draft Preview Example](https://github.com/payloadcms/payload/tree/main/examples/draft-preview/payload) demonstrates how to implement [Draft Preview](https://payloadcms.com/docs/admin/preview#draft-preview) in [Payload](https://github.com/payloadcms/payload) using [Versions](https://payloadcms.com/docs/versions/overview) and [Drafts](https://payloadcms.com/docs/versions/drafts). With Draft Preview, you can navigate to your front-end application and enter "draft mode", where your queries are modified to fetch draft content instead of published content. This is useful for seeing how your content will look before being published.

## Quick Start

To spin up this example locally, follow these steps:

1. Run the following command to create a project from the example:

- `npx create-payload-app --example draft-preview`

2. `cp .env.example .env` to copy the example environment variables
3. `pnpm dev`, `yarn dev` or `npm run dev` to start the server
4. `open http://localhost:3000/admin` to access the admin panel
5. Login with email `demo@payloadcms.com` and password `demo`

That's it! Changes made in `./src` will be reflected in your app. See the [Development](#development) section for more details.

## How it works

Draft preview works by sending the user to your front-end with a `secret` along with their http-only cookies. Your front-end catches the request, verifies the authenticity, then enters into it's own preview mode. Once in preview mode, your front-end can begin securely requesting draft documents from Payload. See [Preview Mode](#preview-mode) for more details.

### Collections

See the [Collections](https://payloadcms.com/docs/configuration/collections) docs for details on how to extend any of this functionality.

- #### Users

  The `users` collection is auth-enabled which provides access to the admin panel. When previewing documents on your front-end, the user's JWT is used to authenticate the request. See [Pages](#pages) for more details.

  For additional help with authentication, see the [Authentication](https://payloadcms.com/docs/authentication/overview#authentication-overview) docs or the official [Auth Example](https://github.com/payloadcms/payload/tree/main/examples/auth).

- #### Pages

  The `pages` collection is draft-enabled and has access control that restricts public users from viewing pages with a `_status` of `draft`. To fetch draft documents on your front-end, simply include the `draft=true` query param along with the `Authorization` header once you have entered [Preview Mode](#preview-mode).

  ```ts
  const preview = true // set this based on your own front-end environment (see `Preview Mode` below)
  const pageSlug = 'example-page' // same here
  const searchParams = `?where[slug][equals]=${pageSlug}&depth=1${preview ? `&draft=true` : ''}`

  // when previewing, send the payload token to bypass draft access control
  const pageReq = await fetch(`${process.env.NEXT_PUBLIC_PAYLOAD_URL}/api/pages${searchParams}`, {
    headers: {
      ...(preview
        ? {
            Authorization: `JWT ${payloadToken}`,
          }
        : {}),
    },
  })
  ```

  For more details on how to extend this functionality, see the [Authentication](https://payloadcms.com/docs/authentication/overview) docs.

### Preview Mode

To preview draft documents, the user first needs to have at least one draft document saved. When they click the "preview" button from the Payload admin panel, a custom [preview function](https://payloadcms.com/docs/admin/collections#preview) routes them to your front-end with a `secret` along with their http-only cookies. An API route on your front-end will verify the secret and token before entering into it's own preview mode. Once in preview mode, it can begin requesting drafts from Payload using the `Authorization` header. See [Pages](#pages) for more details.

> "Preview mode" can vary between frameworks. In the Next.js App Router, [Draft Mode](https://nextjs.org/docs/pages/building-your-application/configuring/draft-mode) enables you to work with previewable content. It provides methods to set cookies in your browser, ensuring content is displayed as a draft, but this behavior might differ in other frameworks.

### On-demand Revalidation

If your front-end is statically generated then you may also want to regenerate the HTML for each page individually as they are published, referred to as On-demand Revalidation. This will prevent your static site from having to fully rebuild every page in order to deploy content changes. To do this, we add an `afterChange` hook to the collection that fires a request to your front-end in the background each time the document is updated. You can handle this request on your front-end to revalidate the HTML for your page.

> On-demand revalidation can vary between frameworks. In the Next.js [App Router](https://nextjs.org/docs/app/building-your-application/data-fetching/revalidating#on-demand-revalidation), on-demand revalidation allows you to regenerate the HTML for specific pages as needed. However, this behavior may differ in other frameworks.

### Admin Bar

You might also want to render an admin bar on your front-end so that logged-in users can quickly navigate between the front-end and Payload as they're editing. For React apps, check out the official [Payload Admin Bar](https://github.com/payloadcms/payload/tree/main/packages/admin-bar). For other frameworks, simply hit the `/me` route with `credentials: 'include'` and render your own admin bar if the user is logged in.

### CORS

The [`cors`](https://payloadcms.com/docs/production/preventing-abuse#cross-origin-resource-sharing-cors), [`csrf`](https://payloadcms.com/docs/production/preventing-abuse#cross-site-request-forgery-csrf), and [`cookies`](https://payloadcms.com/docs/authentication/overview#options) settings are configured to ensure that the admin panel and front-end can communicate with each other securely. If you are combining your front-end and admin panel into a single application that runs of a shared port and domain, you can simplify your config by removing these settings.

For more details on this, see the [CORS](https://payloadcms.com/docs/production/preventing-abuse#cross-origin-resource-sharing-cors) docs.

## Development

To spin up this example locally, follow the [Quick Start](#quick-start).

### Seed

On boot, a seed script is included to scaffold a basic database for you to use as an example. You can remove `pnpm seed` from the `dev` script in the `package.json` to prevent this behavior. You can also freshly seed your project at any time by running `pnpm seed`. This seed creates a user with email `demo@payloadcms.com` and password `demo` along with a home page and an example page with two versions, one published and the other draft.

> NOTICE: seeding the database is destructive because it drops your current database to populate a fresh one from the seed template. Only run this command if you are starting a new project or can afford to lose your current data.

## Production

To run Payload in production, you need to build and start the Admin panel. To do so, follow these steps:

1. Invoke the `next build` script by running `pnpm build` or `npm run build` in your project root. This creates a `.next` directory with a production-ready admin bundle.
1. Finally run `pnpm start` or `npm run start` to run Node in production and serve Payload from the `.build` directory.

### Deployment

The easiest way to deploy your project is to use [Payload Cloud](https://payloadcms.com/new/import), a one-click hosting solution to deploy production-ready instances of your Payload apps directly from your GitHub repo. You can also choose to self-host your app, check out the [Deployment](https://payloadcms.com/docs/production/deployment) docs for more details.

## Questions

If you have any issues or questions, reach out to us on [Discord](https://discord.com/invite/payload) or start a [GitHub discussion](https://github.com/payloadcms/payload/discussions).
````

## File: tsconfig.json
````json
{
  "compilerOptions": {
    "baseUrl": ".",
    "esModuleInterop": true,
    "target": "ES2022",
    "lib": [
      "DOM",
      "DOM.Iterable",
      "ES2022"
    ],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": false,
    "strictNullChecks": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "incremental": true,
    "jsx": "preserve",
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "sourceMap": true,
    "isolatedModules": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@payload-config": [
        "./src/payload.config.ts"
      ],
      "@payload-types": [
        "./src/payload-types.ts"
      ],
      "react": [
        "./node_modules/@types/react"
      ],
      "@/*": [
        "./src/*"
      ],
    }
  },
  "include": [
    "**/*.ts",
    "**/*.tsx",
    ".next/types/**/*.ts",
    "redirects.js",
    "next.config.mjs"
  ],
  "exclude": [
    "node_modules"
  ]
}
````
