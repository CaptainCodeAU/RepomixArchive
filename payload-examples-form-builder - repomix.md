This file is a merged representation of `payload/examples/form-builder/` codebase (https://github.com/payloadcms/payload/tree/main/examples/form-builder), combined into a single document by Repomix. The Payload Form Builder Example demonstrates how to implement the official Form Builder Plugin in Payload. (Please note that this example includes a fully integrated Next.js App Router front-end that runs on the same server as Payload.)

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
        page.tsx
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
  blocks/
    Form/
      index.ts
  collections/
    Pages/
      access/
        publishedOnly.ts
      index.ts
    Users.ts
  components/
    Blocks/
      Form/
        Checkbox/
          index.module.scss
          index.tsx
        Country/
          index.module.scss
          index.tsx
          options.ts
        Email/
          index.module.scss
          index.tsx
        Error/
          index.module.scss
          index.tsx
        Message/
          index.module.scss
          index.tsx
        Number/
          index.module.scss
          index.tsx
        Select/
          index.module.scss
          index.tsx
        State/
          index.module.scss
          index.tsx
          options.ts
        Text/
          index.module.scss
          index.tsx
        Textarea/
          index.module.scss
          index.tsx
        Width/
          index.module.scss
          index.tsx
        buildInitialFormState.tsx
        fields.tsx
        index.module.scss
        index.tsx
        shared.scss
      index.tsx
    Button/
      index.module.scss
      index.tsx
    CloseModalOnRouteChange/
      index.tsx
    Gutter/
      index.module.scss
      index.tsx
    Header/
      index.module.scss
      index.tsx
      mobileMenuModal.module.scss
      MobileMenuModal.tsx
    icons/
      Check/
        index.module.scss
        index.tsx
      Menu/
        index.tsx
    Link/
      index.tsx
    Logo/
      index.tsx
    RichText/
      index.tsx
      nodeFormat.tsx
      serialize.tsx
    VerticalPadding/
      index.module.scss
      index.tsx
  css/
    app.scss
    colors.scss
    common.scss
    queries.scss
    type.scss
  fields/
    link.ts
    slug.ts
  globals/
    MainMenu.ts
  migrations/
    seed.ts
  seed/
    advanced.ts
    advancedForm.ts
    basicForm.ts
    contact.ts
    contactForm.ts
    home.ts
    signUp.ts
    signUpForm.ts
  utilities/
    deepMerge.ts
    formatSlug.ts
    getGlobals.ts
    toKebabCase.ts
  payload-types.ts
  payload.config.ts
.env.example
.eslintrc.cjs
.gitignore
.prettierrc.json
next.config.mjs
package.json
README.md
tsconfig.json
```

# Files

## File: src/app/(app)/[slug]/page.tsx
```typescript
import config from '../../../payload.config'
import { notFound } from 'next/navigation'
import { getPayload } from 'payload'
import React from 'react'

import type { Page as PageType } from '../../../payload-types'

import Blocks from '../../../components/Blocks'

interface PageParams {
  params: Promise<{
    slug?: string
  }>
}

// eslint-disable-next-line no-restricted-exports
export default async function Page({ params: paramsPromise }: PageParams) {
  const { slug = 'home' } = await paramsPromise
  const payload = await getPayload({ config })

  const pageRes = await payload.find({
    collection: 'pages',
    draft: false,
    limit: 1,
    overrideAccess: false,
    where: {
      slug: {
        equals: slug,
      },
    },
  })

  const page = pageRes?.docs?.[0] as null | PageType

  if (page === null) {
    return notFound()
  }

  return (
    <React.Fragment>
      <Blocks blocks={page.layout} />
    </React.Fragment>
  )
}

export async function generateStaticParams() {
  const payload = await getPayload({ config })
  const pagesRes = await payload.find({
    collection: 'pages',
    draft: false,
    limit: 100,
    overrideAccess: false,
  })

  const pages = pagesRes?.docs

  return pages.map(({ slug }) =>
    slug !== 'home'
      ? {
          slug,
        }
      : {},
  )
}
```

## File: src/app/(app)/layout.tsx
```typescript
import { ModalContainer, ModalProvider } from '@faceless-ui/modal'
import React from 'react'

import { CloseModalOnRouteChange } from '../../components/CloseModalOnRouteChange'
import { Header } from '../../components/Header'
import '../../css/app.scss'

export const metadata = {
  description: 'An example of how to authenticate with Payload from a Next.js app.',
  title: 'Payload Auth + Next.js App Router Example',
}

export default function RootLayout(props: { children: React.ReactNode }) {
  const { children } = props

  return (
    <html lang="en">
      <body>
        <React.Fragment>
          <ModalProvider classPrefix="form" transTime={0} zIndex="var(--modal-z-index)">
            <CloseModalOnRouteChange />
            <Header />
            {/* <Component {...pageProps} /> */}
            {children}
            <ModalContainer />
          </ModalProvider>
        </React.Fragment>
      </body>
    </html>
  )
}
```

## File: src/app/(app)/page.tsx
```typescript
import Page from './[slug]/page'

export default Page
```

## File: src/app/(payload)/admin/[[...segments]]/not-found.tsx
```typescript
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
```

## File: src/app/(payload)/admin/[[...segments]]/page.tsx
```typescript
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
```

## File: src/app/(payload)/admin/importMap.js
```javascript
import { RscEntryLexicalCell as RscEntryLexicalCell_44fe37237e0ebf4470c9990d8cb7b07e } from '@payloadcms/richtext-lexical/rsc'
import { RscEntryLexicalField as RscEntryLexicalField_44fe37237e0ebf4470c9990d8cb7b07e } from '@payloadcms/richtext-lexical/rsc'
import { HeadingFeatureClient as HeadingFeatureClient_e70f5e05f09f93e00b997edb1ef0c864 } from '@payloadcms/richtext-lexical/client'
import { BoldFeatureClient as BoldFeatureClient_e70f5e05f09f93e00b997edb1ef0c864 } from '@payloadcms/richtext-lexical/client'
import { ItalicFeatureClient as ItalicFeatureClient_e70f5e05f09f93e00b997edb1ef0c864 } from '@payloadcms/richtext-lexical/client'
import { LinkFeatureClient as LinkFeatureClient_e70f5e05f09f93e00b997edb1ef0c864 } from '@payloadcms/richtext-lexical/client'
import { FixedToolbarFeatureClient as FixedToolbarFeatureClient_e70f5e05f09f93e00b997edb1ef0c864 } from '@payloadcms/richtext-lexical/client'
import { InlineToolbarFeatureClient as InlineToolbarFeatureClient_e70f5e05f09f93e00b997edb1ef0c864 } from '@payloadcms/richtext-lexical/client'

export const importMap = {
  '@payloadcms/richtext-lexical/rsc#RscEntryLexicalCell':
    RscEntryLexicalCell_44fe37237e0ebf4470c9990d8cb7b07e,
  '@payloadcms/richtext-lexical/rsc#RscEntryLexicalField':
    RscEntryLexicalField_44fe37237e0ebf4470c9990d8cb7b07e,
  '@payloadcms/richtext-lexical/client#HeadingFeatureClient':
    HeadingFeatureClient_e70f5e05f09f93e00b997edb1ef0c864,
  '@payloadcms/richtext-lexical/client#BoldFeatureClient':
    BoldFeatureClient_e70f5e05f09f93e00b997edb1ef0c864,
  '@payloadcms/richtext-lexical/client#ItalicFeatureClient':
    ItalicFeatureClient_e70f5e05f09f93e00b997edb1ef0c864,
  '@payloadcms/richtext-lexical/client#LinkFeatureClient':
    LinkFeatureClient_e70f5e05f09f93e00b997edb1ef0c864,
  '@payloadcms/richtext-lexical/client#FixedToolbarFeatureClient':
    FixedToolbarFeatureClient_e70f5e05f09f93e00b997edb1ef0c864,
  '@payloadcms/richtext-lexical/client#InlineToolbarFeatureClient':
    InlineToolbarFeatureClient_e70f5e05f09f93e00b997edb1ef0c864,
}
```

## File: src/app/(payload)/api/[...slug]/route.ts
```typescript
/* THIS FILE WAS GENERATED AUTOMATICALLY BY PAYLOAD. */
/* DO NOT MODIFY IT BECAUSE IT COULD BE REWRITTEN AT ANY TIME. */
import config from '@payload-config'
import { REST_DELETE, REST_GET, REST_OPTIONS, REST_PATCH, REST_POST } from '@payloadcms/next/routes'

export const GET = REST_GET(config)
export const POST = REST_POST(config)
export const DELETE = REST_DELETE(config)
export const PATCH = REST_PATCH(config)
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
import { GRAPHQL_PLAYGROUND_GET } from '@payloadcms/next/routes'

export const GET = GRAPHQL_PLAYGROUND_GET(config)
```

## File: src/app/(payload)/layout.tsx
```typescript
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
```

## File: src/blocks/Form/index.ts
```typescript
import type { Block } from 'payload'

import { HeadingFeature, lexicalEditor } from '@payloadcms/richtext-lexical'

export const FormBlock: Block = {
  slug: 'formBlock',
  fields: [
    {
      name: 'form',
      type: 'relationship',
      relationTo: 'forms',
      required: true,
    },
    {
      name: 'enableIntro',
      type: 'checkbox',
      label: 'Enable Intro Content',
    },
    {
      name: 'introContent',
      type: 'richText',
      admin: {
        condition: (_, { enableIntro }) => Boolean(enableIntro),
      },
      editor: lexicalEditor({
        features: ({ rootFeatures }) => {
          return [...rootFeatures, HeadingFeature({ enabledHeadingSizes: ['h1', 'h2', 'h3'] })]
        },
      }),
      label: 'Intro Content',
    },
  ],
  graphQL: {
    singularName: 'FormBlock',
  },
  labels: {
    plural: 'Form Blocks',
    singular: 'Form Block',
  },
}
```

## File: src/collections/Pages/access/publishedOnly.ts
```typescript
import type { Access } from 'payload'

export const publishedOnly: Access = ({ req: { user } }) => {
  if (user) {
    return true
  }

  return {
    _status: {
      equals: 'published',
    },
  }
}
```

## File: src/collections/Pages/index.ts
```typescript
import type { CollectionConfig } from 'payload'

import { FormBlock } from '../../blocks/Form'
import { slugField } from '../../fields/slug'
import { publishedOnly } from './access/publishedOnly'

export const Pages: CollectionConfig = {
  slug: 'pages',
  access: {
    read: publishedOnly,
  },
  admin: {
    defaultColumns: ['title', 'slug', 'updatedAt'],
    useAsTitle: 'title',
  },
  fields: [
    {
      name: 'title',
      type: 'text',
      required: true,
    },
    {
      type: 'tabs',
      tabs: [
        {
          fields: [
            {
              name: 'layout',
              type: 'blocks',
              blocks: [FormBlock],
              required: true,
            },
          ],
          label: 'Content',
        },
      ],
    },
    slugField(),
  ],
  versions: {
    drafts: true,
  },
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
    // Don't need any user fields here
  ],
}
```

## File: src/components/Blocks/Form/Checkbox/index.module.scss
```scss
@use '../shared.scss';

.checkbox {
  position: relative;
  margin-bottom: var(--base);

  :global {
    button {
      border: 0;
      background: none;
      box-shadow: none;
      border-radius: 0;
      padding: 0;
      display: flex;
      align-items: center;
      cursor: pointer;
      font-size: 1rem;

      &:focus,
      &:active {
        outline: none;
      }

      &:hover {
        svg {
          opacity: 0.2;
        }
      }
    }

    input {
      position: absolute;
      top: 0;
      left: 0;
      opacity: 0;
    }
  }
}

.container {
  display: flex;
}

.input {
  @include shared.formInput;
  padding: 0;
  line-height: 0;
  position: relative;
  width: var(--base);
  height: var(--base);
  margin-right: calc(var(--base) * 0.5);
  margin-bottom: 0;

  svg {
    opacity: 0;
  }
}

.checked {
  :global {
    svg {
      opacity: 1 !important;
    }
  }
}

.label {
  display: block;
}
```

## File: src/components/Blocks/Form/Checkbox/index.tsx
```typescript
import type { CheckboxField } from '@payloadcms/plugin-form-builder/types'
import type { FieldErrorsImpl, FieldValues, UseFormRegister } from 'react-hook-form'

import React, { useState } from 'react'

import { Check } from '../../../icons/Check'
import { Error } from '../Error'
import { Width } from '../Width'
import classes from './index.module.scss'

export const Checkbox: React.FC<
  {
    errors: Partial<
      FieldErrorsImpl<{
        [x: string]: any
      }>
    >
    getValues: any
    register: UseFormRegister<any & FieldValues>
    setValue: any
  } & CheckboxField
> = ({
  name,
  errors,
  getValues,
  label,
  register,
  required: requiredFromProps,
  setValue,
  width,
}) => {
  const [checked, setChecked] = useState(false)

  const isCheckboxChecked = getValues(name)

  return (
    <Width width={width}>
      <div className={[classes.checkbox, checked && classes.checked].filter(Boolean).join(' ')}>
        <div className={classes.container}>
          <input
            type="checkbox"
            {...register(name, { required: requiredFromProps })}
            checked={isCheckboxChecked}
          />
          <button
            onClick={() => {
              setValue(name, !checked)
              setChecked(!checked)
            }}
            type="button"
          >
            <span className={classes.input}>
              <Check />
            </span>
          </button>
          <span className={classes.label}>{label}</span>
        </div>
        {requiredFromProps && errors[name] && checked === false && <Error />}
      </div>
    </Width>
  )
}
```

## File: src/components/Blocks/Form/Country/index.module.scss
```scss
@use '../shared.scss';

.select {
  position: relative;
  margin-bottom: var(--base);
}

.label {
  margin-bottom: 10px;
  display: block;
}

.reactSelect {
  display: flex;

  :global {
    div.rs__control {
      @include shared.formInput;
      height: auto;
    }

    .rs__input-container {
      color: var(--color-black);
    }

    .rs__value-container {
      padding: 0;
      > * {
        margin-top: 0;
        margin-bottom: 0;
        padding-top: 0;
        padding-bottom: 0;
      }
    }

    .rs__single-value {
      color: var(--color-black);
    }

    .rs__indicators {
      position: absolute;
      top: calc(var(--base) * 0.9);
      right: calc(var(--base) * 0.9);
      .arrow {
        transform: rotate(90deg);
      }
    }

    .rs__indicator {
      padding: 0px 4px;
      cursor: pointer;

      svg path {
        fill: var(--color-dark-gray);
      }

      &:hover {
        svg path {
          fill: var(--color-dark-gray);
        }
      }
    }

    .rs__indicator-separator {
      display: none;
    }

    .rs__menu {
      color: var(--color-black);
      background-color: var(--color-white);
      z-index: 2;
      border-radius: 0;
      box-shadow: 0 4px 11px hsl(0deg 0% 0% / 10%);
    }

    .rs__menu-list {
      padding: calc(var(--base) / 4) 0;
    }

    .rs__group-heading {
      margin-bottom: calc(var(--base) / 2);
    }

    .rs__option {
      font-size: 1rem;
      padding: calc(var(--base) / 2) var(--base);

      &--is-focused {
        background-color: var(--color-light-gray);
        color: var(--color-black);
      }

      &--is-selected {
        background-color: var(--color-light-gray);
        color: var(--color-black);
      }
    }

    .rs__multi-value {
      padding: 0;
      background: var(--color-light-gray);
    }

    .rs__multi-value__label {
      max-width: 150px;
      color: var(--color-black);
      padding: calc(var(--base) / 8) calc(var(--base) / 4);
    }

    .rs__multi-value__remove {
      cursor: pointer;

      &:hover {
        color: var(--color-black);
        background: var(--color-light-gray);
      }
    }

    .rs__clear-indicator {
      cursor: pointer;
    }
  }
}
```

## File: src/components/Blocks/Form/Country/index.tsx
```typescript
import type { CountryField } from '@payloadcms/plugin-form-builder/types'
import type { Control, FieldErrorsImpl, FieldValues } from 'react-hook-form'

import React from 'react'
import { Controller } from 'react-hook-form'
import ReactSelect from 'react-select'

import { Error } from '../Error'
import { Width } from '../Width'
import classes from './index.module.scss'
import { countryOptions } from './options'

export const Country: React.FC<
  {
    control: Control<FieldValues, any>
    errors: Partial<
      FieldErrorsImpl<{
        [x: string]: any
      }>
    >
  } & CountryField
> = ({ name, control, errors, label, required, width }) => {
  return (
    <Width width={width}>
      <div className={classes.select}>
        <label className={classes.label} htmlFor={name}>
          {label}
        </label>
        <Controller
          control={control}
          defaultValue=""
          name={name}
          render={({ field: { onChange, value } }) => (
            <ReactSelect
              className={classes.reactSelect}
              classNamePrefix="rs"
              inputId={name}
              instanceId={name}
              onChange={(val) => onChange(val ? val.value : '')}
              options={countryOptions}
              value={countryOptions.find((c) => c.value === value)}
            />
          )}
          rules={{ required }}
        />
        {required && errors[name] && <Error />}
      </div>
    </Width>
  )
}
```

## File: src/components/Blocks/Form/Country/options.ts
```typescript
export const countryOptions = [
  {
    label: 'Afghanistan',
    value: 'AF',
  },
  {
    label: 'Åland Islands',
    value: 'AX',
  },
  {
    label: 'Albania',
    value: 'AL',
  },
  {
    label: 'Algeria',
    value: 'DZ',
  },
  {
    label: 'American Samoa',
    value: 'AS',
  },
  {
    label: 'Andorra',
    value: 'AD',
  },
  {
    label: 'Angola',
    value: 'AO',
  },
  {
    label: 'Anguilla',
    value: 'AI',
  },
  {
    label: 'Antarctica',
    value: 'AQ',
  },
  {
    label: 'Antigua and Barbuda',
    value: 'AG',
  },
  {
    label: 'Argentina',
    value: 'AR',
  },
  {
    label: 'Armenia',
    value: 'AM',
  },
  {
    label: 'Aruba',
    value: 'AW',
  },
  {
    label: 'Australia',
    value: 'AU',
  },
  {
    label: 'Austria',
    value: 'AT',
  },
  {
    label: 'Azerbaijan',
    value: 'AZ',
  },
  {
    label: 'Bahamas',
    value: 'BS',
  },
  {
    label: 'Bahrain',
    value: 'BH',
  },
  {
    label: 'Bangladesh',
    value: 'BD',
  },
  {
    label: 'Barbados',
    value: 'BB',
  },
  {
    label: 'Belarus',
    value: 'BY',
  },
  {
    label: 'Belgium',
    value: 'BE',
  },
  {
    label: 'Belize',
    value: 'BZ',
  },
  {
    label: 'Benin',
    value: 'BJ',
  },
  {
    label: 'Bermuda',
    value: 'BM',
  },
  {
    label: 'Bhutan',
    value: 'BT',
  },
  {
    label: 'Bolivia',
    value: 'BO',
  },
  {
    label: 'Bosnia and Herzegovina',
    value: 'BA',
  },
  {
    label: 'Botswana',
    value: 'BW',
  },
  {
    label: 'Bouvet Island',
    value: 'BV',
  },
  {
    label: 'Brazil',
    value: 'BR',
  },
  {
    label: 'British Indian Ocean Territory',
    value: 'IO',
  },
  {
    label: 'Brunei Darussalam',
    value: 'BN',
  },
  {
    label: 'Bulgaria',
    value: 'BG',
  },
  {
    label: 'Burkina Faso',
    value: 'BF',
  },
  {
    label: 'Burundi',
    value: 'BI',
  },
  {
    label: 'Cambodia',
    value: 'KH',
  },
  {
    label: 'Cameroon',
    value: 'CM',
  },
  {
    label: 'Canada',
    value: 'CA',
  },
  {
    label: 'Cape Verde',
    value: 'CV',
  },
  {
    label: 'Cayman Islands',
    value: 'KY',
  },
  {
    label: 'Central African Republic',
    value: 'CF',
  },
  {
    label: 'Chad',
    value: 'TD',
  },
  {
    label: 'Chile',
    value: 'CL',
  },
  {
    label: 'China',
    value: 'CN',
  },
  {
    label: 'Christmas Island',
    value: 'CX',
  },
  {
    label: 'Cocos (Keeling) Islands',
    value: 'CC',
  },
  {
    label: 'Colombia',
    value: 'CO',
  },
  {
    label: 'Comoros',
    value: 'KM',
  },
  {
    label: 'Congo',
    value: 'CG',
  },
  {
    label: 'Congo, The Democratic Republic of the',
    value: 'CD',
  },
  {
    label: 'Cook Islands',
    value: 'CK',
  },
  {
    label: 'Costa Rica',
    value: 'CR',
  },
  {
    label: "Cote D'Ivoire",
    value: 'CI',
  },
  {
    label: 'Croatia',
    value: 'HR',
  },
  {
    label: 'Cuba',
    value: 'CU',
  },
  {
    label: 'Cyprus',
    value: 'CY',
  },
  {
    label: 'Czech Republic',
    value: 'CZ',
  },
  {
    label: 'Denmark',
    value: 'DK',
  },
  {
    label: 'Djibouti',
    value: 'DJ',
  },
  {
    label: 'Dominica',
    value: 'DM',
  },
  {
    label: 'Dominican Republic',
    value: 'DO',
  },
  {
    label: 'Ecuador',
    value: 'EC',
  },
  {
    label: 'Egypt',
    value: 'EG',
  },
  {
    label: 'El Salvador',
    value: 'SV',
  },
  {
    label: 'Equatorial Guinea',
    value: 'GQ',
  },
  {
    label: 'Eritrea',
    value: 'ER',
  },
  {
    label: 'Estonia',
    value: 'EE',
  },
  {
    label: 'Ethiopia',
    value: 'ET',
  },
  {
    label: 'Falkland Islands (Malvinas)',
    value: 'FK',
  },
  {
    label: 'Faroe Islands',
    value: 'FO',
  },
  {
    label: 'Fiji',
    value: 'FJ',
  },
  {
    label: 'Finland',
    value: 'FI',
  },
  {
    label: 'France',
    value: 'FR',
  },
  {
    label: 'French Guiana',
    value: 'GF',
  },
  {
    label: 'French Polynesia',
    value: 'PF',
  },
  {
    label: 'French Southern Territories',
    value: 'TF',
  },
  {
    label: 'Gabon',
    value: 'GA',
  },
  {
    label: 'Gambia',
    value: 'GM',
  },
  {
    label: 'Georgia',
    value: 'GE',
  },
  {
    label: 'Germany',
    value: 'DE',
  },
  {
    label: 'Ghana',
    value: 'GH',
  },
  {
    label: 'Gibraltar',
    value: 'GI',
  },
  {
    label: 'Greece',
    value: 'GR',
  },
  {
    label: 'Greenland',
    value: 'GL',
  },
  {
    label: 'Grenada',
    value: 'GD',
  },
  {
    label: 'Guadeloupe',
    value: 'GP',
  },
  {
    label: 'Guam',
    value: 'GU',
  },
  {
    label: 'Guatemala',
    value: 'GT',
  },
  {
    label: 'Guernsey',
    value: 'GG',
  },
  {
    label: 'Guinea',
    value: 'GN',
  },
  {
    label: 'Guinea-Bissau',
    value: 'GW',
  },
  {
    label: 'Guyana',
    value: 'GY',
  },
  {
    label: 'Haiti',
    value: 'HT',
  },
  {
    label: 'Heard Island and Mcdonald Islands',
    value: 'HM',
  },
  {
    label: 'Holy See (Vatican City State)',
    value: 'VA',
  },
  {
    label: 'Honduras',
    value: 'HN',
  },
  {
    label: 'Hong Kong',
    value: 'HK',
  },
  {
    label: 'Hungary',
    value: 'HU',
  },
  {
    label: 'Iceland',
    value: 'IS',
  },
  {
    label: 'India',
    value: 'IN',
  },
  {
    label: 'Indonesia',
    value: 'ID',
  },
  {
    label: 'Iran, Islamic Republic Of',
    value: 'IR',
  },
  {
    label: 'Iraq',
    value: 'IQ',
  },
  {
    label: 'Ireland',
    value: 'IE',
  },
  {
    label: 'Isle of Man',
    value: 'IM',
  },
  {
    label: 'Israel',
    value: 'IL',
  },
  {
    label: 'Italy',
    value: 'IT',
  },
  {
    label: 'Jamaica',
    value: 'JM',
  },
  {
    label: 'Japan',
    value: 'JP',
  },
  {
    label: 'Jersey',
    value: 'JE',
  },
  {
    label: 'Jordan',
    value: 'JO',
  },
  {
    label: 'Kazakhstan',
    value: 'KZ',
  },
  {
    label: 'Kenya',
    value: 'KE',
  },
  {
    label: 'Kiribati',
    value: 'KI',
  },
  {
    label: "Democratic People's Republic of Korea",
    value: 'KP',
  },
  {
    label: 'Korea, Republic of',
    value: 'KR',
  },
  {
    label: 'Kosovo',
    value: 'XK',
  },
  {
    label: 'Kuwait',
    value: 'KW',
  },
  {
    label: 'Kyrgyzstan',
    value: 'KG',
  },
  {
    label: "Lao People's Democratic Republic",
    value: 'LA',
  },
  {
    label: 'Latvia',
    value: 'LV',
  },
  {
    label: 'Lebanon',
    value: 'LB',
  },
  {
    label: 'Lesotho',
    value: 'LS',
  },
  {
    label: 'Liberia',
    value: 'LR',
  },
  {
    label: 'Libyan Arab Jamahiriya',
    value: 'LY',
  },
  {
    label: 'Liechtenstein',
    value: 'LI',
  },
  {
    label: 'Lithuania',
    value: 'LT',
  },
  {
    label: 'Luxembourg',
    value: 'LU',
  },
  {
    label: 'Macao',
    value: 'MO',
  },
  {
    label: 'Macedonia, The Former Yugoslav Republic of',
    value: 'MK',
  },
  {
    label: 'Madagascar',
    value: 'MG',
  },
  {
    label: 'Malawi',
    value: 'MW',
  },
  {
    label: 'Malaysia',
    value: 'MY',
  },
  {
    label: 'Maldives',
    value: 'MV',
  },
  {
    label: 'Mali',
    value: 'ML',
  },
  {
    label: 'Malta',
    value: 'MT',
  },
  {
    label: 'Marshall Islands',
    value: 'MH',
  },
  {
    label: 'Martinique',
    value: 'MQ',
  },
  {
    label: 'Mauritania',
    value: 'MR',
  },
  {
    label: 'Mauritius',
    value: 'MU',
  },
  {
    label: 'Mayotte',
    value: 'YT',
  },
  {
    label: 'Mexico',
    value: 'MX',
  },
  {
    label: 'Micronesia, Federated States of',
    value: 'FM',
  },
  {
    label: 'Moldova, Republic of',
    value: 'MD',
  },
  {
    label: 'Monaco',
    value: 'MC',
  },
  {
    label: 'Mongolia',
    value: 'MN',
  },
  {
    label: 'Montenegro',
    value: 'ME',
  },
  {
    label: 'Montserrat',
    value: 'MS',
  },
  {
    label: 'Morocco',
    value: 'MA',
  },
  {
    label: 'Mozambique',
    value: 'MZ',
  },
  {
    label: 'Myanmar',
    value: 'MM',
  },
  {
    label: 'Namibia',
    value: 'NA',
  },
  {
    label: 'Nauru',
    value: 'NR',
  },
  {
    label: 'Nepal',
    value: 'NP',
  },
  {
    label: 'Netherlands',
    value: 'NL',
  },
  {
    label: 'Netherlands Antilles',
    value: 'AN',
  },
  {
    label: 'New Caledonia',
    value: 'NC',
  },
  {
    label: 'New Zealand',
    value: 'NZ',
  },
  {
    label: 'Nicaragua',
    value: 'NI',
  },
  {
    label: 'Niger',
    value: 'NE',
  },
  {
    label: 'Nigeria',
    value: 'NG',
  },
  {
    label: 'Niue',
    value: 'NU',
  },
  {
    label: 'Norfolk Island',
    value: 'NF',
  },
  {
    label: 'Northern Mariana Islands',
    value: 'MP',
  },
  {
    label: 'Norway',
    value: 'NO',
  },
  {
    label: 'Oman',
    value: 'OM',
  },
  {
    label: 'Pakistan',
    value: 'PK',
  },
  {
    label: 'Palau',
    value: 'PW',
  },
  {
    label: 'Palestinian Territory, Occupied',
    value: 'PS',
  },
  {
    label: 'Panama',
    value: 'PA',
  },
  {
    label: 'Papua New Guinea',
    value: 'PG',
  },
  {
    label: 'Paraguay',
    value: 'PY',
  },
  {
    label: 'Peru',
    value: 'PE',
  },
  {
    label: 'Philippines',
    value: 'PH',
  },
  {
    label: 'Pitcairn',
    value: 'PN',
  },
  {
    label: 'Poland',
    value: 'PL',
  },
  {
    label: 'Portugal',
    value: 'PT',
  },
  {
    label: 'Puerto Rico',
    value: 'PR',
  },
  {
    label: 'Qatar',
    value: 'QA',
  },
  {
    label: 'Reunion',
    value: 'RE',
  },
  {
    label: 'Romania',
    value: 'RO',
  },
  {
    label: 'Russian Federation',
    value: 'RU',
  },
  {
    label: 'Rwanda',
    value: 'RW',
  },
  {
    label: 'Saint Helena',
    value: 'SH',
  },
  {
    label: 'Saint Kitts and Nevis',
    value: 'KN',
  },
  {
    label: 'Saint Lucia',
    value: 'LC',
  },
  {
    label: 'Saint Pierre and Miquelon',
    value: 'PM',
  },
  {
    label: 'Saint Vincent and the Grenadines',
    value: 'VC',
  },
  {
    label: 'Samoa',
    value: 'WS',
  },
  {
    label: 'San Marino',
    value: 'SM',
  },
  {
    label: 'Sao Tome and Principe',
    value: 'ST',
  },
  {
    label: 'Saudi Arabia',
    value: 'SA',
  },
  {
    label: 'Senegal',
    value: 'SN',
  },
  {
    label: 'Serbia',
    value: 'RS',
  },
  {
    label: 'Seychelles',
    value: 'SC',
  },
  {
    label: 'Sierra Leone',
    value: 'SL',
  },
  {
    label: 'Singapore',
    value: 'SG',
  },
  {
    label: 'Slovakia',
    value: 'SK',
  },
  {
    label: 'Slovenia',
    value: 'SI',
  },
  {
    label: 'Solomon Islands',
    value: 'SB',
  },
  {
    label: 'Somalia',
    value: 'SO',
  },
  {
    label: 'South Africa',
    value: 'ZA',
  },
  {
    label: 'South Georgia and the South Sandwich Islands',
    value: 'GS',
  },
  {
    label: 'Spain',
    value: 'ES',
  },
  {
    label: 'Sri Lanka',
    value: 'LK',
  },
  {
    label: 'Sudan',
    value: 'SD',
  },
  {
    label: 'Suriname',
    value: 'SR',
  },
  {
    label: 'Svalbard and Jan Mayen',
    value: 'SJ',
  },
  {
    label: 'Swaziland',
    value: 'SZ',
  },
  {
    label: 'Sweden',
    value: 'SE',
  },
  {
    label: 'Switzerland',
    value: 'CH',
  },
  {
    label: 'Syrian Arab Republic',
    value: 'SY',
  },
  {
    label: 'Taiwan',
    value: 'TW',
  },
  {
    label: 'Tajikistan',
    value: 'TJ',
  },
  {
    label: 'Tanzania, United Republic of',
    value: 'TZ',
  },
  {
    label: 'Thailand',
    value: 'TH',
  },
  {
    label: 'Timor-Leste',
    value: 'TL',
  },
  {
    label: 'Togo',
    value: 'TG',
  },
  {
    label: 'Tokelau',
    value: 'TK',
  },
  {
    label: 'Tonga',
    value: 'TO',
  },
  {
    label: 'Trinidad and Tobago',
    value: 'TT',
  },
  {
    label: 'Tunisia',
    value: 'TN',
  },
  {
    label: 'Turkey',
    value: 'TR',
  },
  {
    label: 'Turkmenistan',
    value: 'TM',
  },
  {
    label: 'Turks and Caicos Islands',
    value: 'TC',
  },
  {
    label: 'Tuvalu',
    value: 'TV',
  },
  {
    label: 'Uganda',
    value: 'UG',
  },
  {
    label: 'Ukraine',
    value: 'UA',
  },
  {
    label: 'United Arab Emirates',
    value: 'AE',
  },
  {
    label: 'United Kingdom',
    value: 'GB',
  },
  {
    label: 'United States',
    value: 'US',
  },
  {
    label: 'United States Minor Outlying Islands',
    value: 'UM',
  },
  {
    label: 'Uruguay',
    value: 'UY',
  },
  {
    label: 'Uzbekistan',
    value: 'UZ',
  },
  {
    label: 'Vanuatu',
    value: 'VU',
  },
  {
    label: 'Venezuela',
    value: 'VE',
  },
  {
    label: 'Viet Nam',
    value: 'VN',
  },
  {
    label: 'Virgin Islands, British',
    value: 'VG',
  },
  {
    label: 'Virgin Islands, U.S.',
    value: 'VI',
  },
  {
    label: 'Wallis and Futuna',
    value: 'WF',
  },
  {
    label: 'Western Sahara',
    value: 'EH',
  },
  {
    label: 'Yemen',
    value: 'YE',
  },
  {
    label: 'Zambia',
    value: 'ZM',
  },
  {
    label: 'Zimbabwe',
    value: 'ZW',
  },
]
```

## File: src/components/Blocks/Form/Email/index.module.scss
```scss
@use '../shared.scss';

.wrap {
  position: relative;
  margin-bottom: var(--base);
}

.input {
  @include shared.formInput;
}

.label {
  margin-bottom: 10px;
  display: block;
}
```

## File: src/components/Blocks/Form/Email/index.tsx
```typescript
import type { EmailField } from '@payloadcms/plugin-form-builder/types'
import type { FieldErrorsImpl, FieldValues, UseFormRegister } from 'react-hook-form'

import React from 'react'

import { Error } from '../Error'
import { Width } from '../Width'
import classes from './index.module.scss'

export const Email: React.FC<
  {
    errors: Partial<
      FieldErrorsImpl<{
        [x: string]: any
      }>
    >
    register: UseFormRegister<any & FieldValues>
  } & EmailField
> = ({ name, errors, label, register, required: requiredFromProps, width }) => {
  return (
    <Width width={width}>
      <div className={classes.wrap}>
        <label className={classes.label} htmlFor={name}>
          {label}
        </label>
        <input
          className={classes.input}
          id={name}
          placeholder="Email"
          type="text"
          {...register(name, { pattern: /^\S[^\s@]*@\S+$/, required: requiredFromProps })}
        />
        {requiredFromProps && errors[name] && <Error />}
      </div>
    </Width>
  )
}
```

## File: src/components/Blocks/Form/Error/index.module.scss
```scss
.error {
  margin-top: 5px;
  color: var(--color-red);
}
```

## File: src/components/Blocks/Form/Error/index.tsx
```typescript
import * as React from 'react'

import classes from './index.module.scss'

export const Error: React.FC = () => {
  return <div className={classes.error}>This field is required</div>
}
```

## File: src/components/Blocks/Form/Message/index.module.scss
```scss
@use '../.../../../../../css/queries.scss' as *;

.message {
  margin: var(--base) 0 var(--base) 0;

  @include mid-break {
    margin: calc(var(--base) * 0.5) 0 calc(var(--base) * 0.5) 0;
  }
}
```

## File: src/components/Blocks/Form/Message/index.tsx
```typescript
import type { MessageField } from '@payloadcms/plugin-form-builder/types'

import React from 'react'

import RichText from '../../../RichText'
import { Width } from '../Width'
import classes from './index.module.scss'

export const Message: React.FC<MessageField> = ({ message }) => {
  return (
    <Width width={100}>
      <RichText className={classes.message} content={message} />
    </Width>
  )
}
```

## File: src/components/Blocks/Form/Number/index.module.scss
```scss
@use '../shared.scss';

.wrap {
  position: relative;
  margin-bottom: var(--base);
}

.input {
  @include shared.formInput;
}

.label {
  margin-bottom: 10px;
  display: block;
}
```

## File: src/components/Blocks/Form/Number/index.tsx
```typescript
import type { TextField } from '@payloadcms/plugin-form-builder/types'
import type { FieldErrorsImpl, FieldValues, UseFormRegister } from 'react-hook-form'

import React from 'react'

import { Error } from '../Error'
import { Width } from '../Width'
import classes from './index.module.scss'

export const Number: React.FC<
  {
    errors: Partial<
      FieldErrorsImpl<{
        [x: string]: any
      }>
    >
    register: UseFormRegister<any & FieldValues>
  } & TextField
> = ({ name, errors, label, register, required: requiredFromProps, width }) => {
  return (
    <Width width={width}>
      <div className={classes.wrap}>
        <label className={classes.label} htmlFor={name}>
          {label}
        </label>
        <input
          className={classes.input}
          id={name}
          type="number"
          {...register(name, { required: requiredFromProps })}
        />
        {requiredFromProps && errors[name] && <Error />}
      </div>
    </Width>
  )
}
```

## File: src/components/Blocks/Form/Select/index.module.scss
```scss
@use '../shared.scss';

.select {
  position: relative;
  margin-bottom: var(--base);
}

.label {
  margin-bottom: 10px;
  display: block;
}

.reactSelect {
  display: flex;

  :global {
    div.rs__control {
      @include shared.formInput;
      height: auto;
    }

    .rs__input-container {
      color: var(--color-black);
    }

    .rs__value-container {
      padding: 0;
      > * {
        margin-top: 0;
        margin-bottom: 0;
        padding-top: 0;
        padding-bottom: 0;
      }
    }

    .rs__single-value {
      color: var(--color-black);
    }

    .rs__indicators {
      position: absolute;
      top: calc(var(--base) * 0.9);
      right: calc(var(--base) * 0.9);
      .arrow {
        transform: rotate(90deg);
      }
    }

    .rs__indicator {
      padding: 0px 4px;
      cursor: pointer;

      svg path {
        fill: var(--color-dark-gray);
      }

      &:hover {
        svg path {
          fill: var(--color-dark-gray);
        }
      }
    }

    .rs__indicator-separator {
      display: none;
    }

    .rs__menu {
      color: var(--color-black);
      background-color: var(--color-white);
      z-index: 2;
      border-radius: 0;
      box-shadow: 0 4px 11px hsl(0deg 0% 0% / 10%);
    }

    .rs__menu-list {
      padding: calc(var(--base) / 4) 0;
    }

    .rs__group-heading {
      margin-bottom: calc(var(--base) / 2);
    }

    .rs__option {
      font-size: 1rem;
      padding: calc(var(--base) / 2) var(--base);

      &--is-focused {
        background-color: var(--color-light-gray);
        color: var(--color-black);
      }

      &--is-selected {
        background-color: var(--color-light-gray);
        color: var(--color-black);
      }
    }

    .rs__multi-value {
      padding: 0;
      background: var(--color-light-gray);
    }

    .rs__multi-value__label {
      max-width: 150px;
      color: var(--color-black);
      padding: calc(var(--base) / 8) calc(var(--base) / 4);
    }

    .rs__multi-value__remove {
      cursor: pointer;

      &:hover {
        color: var(--color-black);
        background: var(--color-light-gray);
      }
    }

    .rs__clear-indicator {
      cursor: pointer;
    }
  }
}
```

## File: src/components/Blocks/Form/Select/index.tsx
```typescript
import type { SelectField } from '@payloadcms/plugin-form-builder/types'
import type { Control, FieldErrorsImpl, FieldValues } from 'react-hook-form'

import React from 'react'
import { Controller } from 'react-hook-form'
import ReactSelect from 'react-select'

import { Error } from '../Error'
import { Width } from '../Width'
import classes from './index.module.scss'

export const Select: React.FC<
  {
    control: Control<FieldValues, any>
    errors: Partial<
      FieldErrorsImpl<{
        [x: string]: any
      }>
    >
  } & SelectField
> = ({ name, control, errors, label, options, required, width }) => {
  return (
    <Width width={width}>
      <div className={classes.select}>
        <label className={classes.label} htmlFor={name}>
          {label}
        </label>
        <Controller
          control={control}
          defaultValue=""
          name={name}
          render={({ field: { onChange, value } }) => (
            <ReactSelect
              className={classes.reactSelect}
              classNamePrefix="rs"
              inputId={name}
              instanceId={name}
              onChange={(val) => onChange(val ? val.value : '')}
              options={options}
              value={options.find((s) => s.value === value)}
            />
          )}
          rules={{ required }}
        />
        {required && errors[name] && <Error />}
      </div>
    </Width>
  )
}
```

## File: src/components/Blocks/Form/State/index.module.scss
```scss
@use '../shared.scss';

.select {
  position: relative;
  margin-bottom: var(--base);
}

.label {
  margin-bottom: 10px;
  display: block;
}

.reactSelect {
  display: flex;

  :global {
    div.rs__control {
      @include shared.formInput;
      height: auto;
    }

    .rs__input-container {
      color: var(--color-black);
    }

    .rs__value-container {
      padding: 0;
      > * {
        margin-top: 0;
        margin-bottom: 0;
        padding-top: 0;
        padding-bottom: 0;
      }
    }

    .rs__single-value {
      color: var(--color-black);
    }

    .rs__indicators {
      position: absolute;
      top: calc(var(--base) * 0.9);
      right: calc(var(--base) * 0.9);
      .arrow {
        transform: rotate(90deg);
      }
    }

    .rs__indicator {
      padding: 0px 4px;
      cursor: pointer;

      svg path {
        fill: var(--color-dark-gray);
      }

      &:hover {
        svg path {
          fill: var(--color-dark-gray);
        }
      }
    }

    .rs__indicator-separator {
      display: none;
    }

    .rs__menu {
      color: var(--color-black);
      background-color: var(--color-white);
      z-index: 2;
      border-radius: 0;
      box-shadow: 0 4px 11px hsl(0deg 0% 0% / 10%);
    }

    .rs__menu-list {
      padding: calc(var(--base) / 4) 0;
    }

    .rs__group-heading {
      margin-bottom: calc(var(--base) / 2);
    }

    .rs__option {
      font-size: 1rem;
      padding: calc(var(--base) / 2) var(--base);

      &--is-focused {
        background-color: var(--color-light-gray);
        color: var(--color-black);
      }

      &--is-selected {
        background-color: var(--color-light-gray);
        color: var(--color-black);
      }
    }

    .rs__multi-value {
      padding: 0;
      background: var(--color-light-gray);
    }

    .rs__multi-value__label {
      max-width: 150px;
      color: var(--color-black);
      padding: calc(var(--base) / 8) calc(var(--base) / 4);
    }

    .rs__multi-value__remove {
      cursor: pointer;

      &:hover {
        color: var(--color-black);
        background: var(--color-light-gray);
      }
    }

    .rs__clear-indicator {
      cursor: pointer;
    }
  }
}
```

## File: src/components/Blocks/Form/State/index.tsx
```typescript
import type { StateField } from '@payloadcms/plugin-form-builder/types'
import type { Control, FieldErrorsImpl, FieldValues } from 'react-hook-form'

import React from 'react'
import { Controller } from 'react-hook-form'
import ReactSelect from 'react-select'

import { Error } from '../Error'
import { Width } from '../Width'
import classes from './index.module.scss'
import { stateOptions } from './options'

export const State: React.FC<
  {
    control: Control<FieldValues, any>
    errors: Partial<
      FieldErrorsImpl<{
        [x: string]: any
      }>
    >
  } & StateField
> = ({ name, control, errors, label, required, width }) => {
  return (
    <Width width={width}>
      <div className={classes.select}>
        <label className={classes.label} htmlFor={name}>
          {label}
        </label>
        <Controller
          control={control}
          defaultValue=""
          name={name}
          render={({ field: { onChange, value } }) => (
            <ReactSelect
              className={classes.reactSelect}
              classNamePrefix="rs"
              id={name}
              instanceId={name}
              onChange={(val) => onChange(val ? val.value : '')}
              options={stateOptions}
              value={stateOptions.find((t) => t.value === value)}
            />
          )}
          rules={{ required }}
        />
        {required && errors[name] && <Error />}
      </div>
    </Width>
  )
}
```

## File: src/components/Blocks/Form/State/options.ts
```typescript
export const stateOptions = [
  { label: 'Alabama', value: 'AL' },
  { label: 'Alaska', value: 'AK' },
  { label: 'Arizona', value: 'AZ' },
  { label: 'Arkansas', value: 'AR' },
  { label: 'California', value: 'CA' },
  { label: 'Colorado', value: 'CO' },
  { label: 'Connecticut', value: 'CT' },
  { label: 'Delaware', value: 'DE' },
  { label: 'Florida', value: 'FL' },
  { label: 'Georgia', value: 'GA' },
  { label: 'Hawaii', value: 'HI' },
  { label: 'Idaho', value: 'ID' },
  { label: 'Illinois', value: 'IL' },
  { label: 'Indiana', value: 'IN' },
  { label: 'Iowa', value: 'IA' },
  { label: 'Kansas', value: 'KS' },
  { label: 'Kentucky', value: 'KY' },
  { label: 'Louisiana', value: 'LA' },
  { label: 'Maine', value: 'ME' },
  { label: 'Maryland', value: 'MD' },
  { label: 'Massachusetts', value: 'MA' },
  { label: 'Michigan', value: 'MI' },
  { label: 'Minnesota', value: 'MN' },
  { label: 'Mississippi', value: 'MS' },
  { label: 'Missouri', value: 'MO' },
  { label: 'Montana', value: 'MT' },
  { label: 'Nebraska', value: 'NE' },
  { label: 'Nevada', value: 'NV' },
  { label: 'New Hampshire', value: 'NH' },
  { label: 'New Jersey', value: 'NJ' },
  { label: 'New Mexico', value: 'NM' },
  { label: 'New York', value: 'NY' },
  { label: 'North Carolina', value: 'NC' },
  { label: 'North Dakota', value: 'ND' },
  { label: 'Ohio', value: 'OH' },
  { label: 'Oklahoma', value: 'OK' },
  { label: 'Oregon', value: 'OR' },
  { label: 'Pennsylvania', value: 'PA' },
  { label: 'Rhode Island', value: 'RI' },
  { label: 'South Carolina', value: 'SC' },
  { label: 'South Dakota', value: 'SD' },
  { label: 'Tennessee', value: 'TN' },
  { label: 'Texas', value: 'TX' },
  { label: 'Utah', value: 'UT' },
  { label: 'Vermont', value: 'VT' },
  { label: 'Virginia', value: 'VA' },
  { label: 'Washington', value: 'WA' },
  { label: 'West Virginia', value: 'WV' },
  { label: 'Wisconsin', value: 'WI' },
  { label: 'Wyoming', value: 'WY' },
]
```

## File: src/components/Blocks/Form/Text/index.module.scss
```scss
@use '../shared.scss';

.wrap {
  position: relative;
  margin-bottom: var(--base);
}

.input {
  @include shared.formInput;
}

.label {
  margin-bottom: 10px;
  display: block;
}
```

## File: src/components/Blocks/Form/Text/index.tsx
```typescript
import type { TextField } from '@payloadcms/plugin-form-builder/types'
import type { FieldErrorsImpl, FieldValues, UseFormRegister } from 'react-hook-form'

import React from 'react'

import { Error } from '../Error'
import { Width } from '../Width'
import classes from './index.module.scss'

export const Text: React.FC<
  {
    errors: Partial<
      FieldErrorsImpl<{
        [x: string]: any
      }>
    >
    register: UseFormRegister<any & FieldValues>
  } & TextField
> = ({ name, errors, label, register, required: requiredFromProps, width }) => {
  return (
    <Width width={width}>
      <div className={classes.wrap}>
        <label className={classes.label} htmlFor={name}>
          {label}
        </label>
        <input
          className={classes.input}
          id={name}
          type="text"
          {...register(name, { required: requiredFromProps })}
        />
        {requiredFromProps && errors[name] && <Error />}
      </div>
    </Width>
  )
}
```

## File: src/components/Blocks/Form/Textarea/index.module.scss
```scss
@use '../shared.scss';

.wrap {
  position: relative;
  margin-bottom: var(--base);
}

.textarea {
  @include shared.formInput;
  height: unset;
}

.label {
  margin-bottom: 10px;
  display: block;
}
```

## File: src/components/Blocks/Form/Textarea/index.tsx
```typescript
import type { TextField } from '@payloadcms/plugin-form-builder/types'
import type { FieldErrorsImpl, FieldValues, UseFormRegister } from 'react-hook-form'

import React from 'react'

import { Error } from '../Error'
import { Width } from '../Width'
import classes from './index.module.scss'

export const Textarea: React.FC<
  {
    errors: Partial<
      FieldErrorsImpl<{
        [x: string]: any
      }>
    >
    register: UseFormRegister<any & FieldValues>
    rows?: number
  } & TextField
> = ({ name, errors, label, register, required: requiredFromProps, rows = 3, width }) => {
  return (
    <Width width={width}>
      <div className={classes.wrap}>
        <label className={classes.label} htmlFor={name}>
          {label}
        </label>
        <textarea
          className={classes.textarea}
          id={name}
          rows={rows}
          {...register(name, { required: requiredFromProps })}
        />
        {requiredFromProps && errors[name] && <Error />}
      </div>
    </Width>
  )
}
```

## File: src/components/Blocks/Form/Width/index.module.scss
```scss
@use '../../../../css/queries.scss' as *;

.width {
  padding-left: calc(var(--base) * 0.5);
  padding-right: calc(var(--base) * 0.5);

  @include mid-break {
    width: 100% !important;
  }
}
```

## File: src/components/Blocks/Form/Width/index.tsx
```typescript
import * as React from 'react'

import classes from './index.module.scss'

export const Width: React.FC<{
  children: React.ReactNode
  width?: number
}> = ({ children, width }) => {
  return (
    <div className={classes.width} style={{ width: width ? `${width}%` : undefined }}>
      {children}
    </div>
  )
}
```

## File: src/components/Blocks/Form/buildInitialFormState.tsx
```typescript
import type { FormFieldBlock } from '@payloadcms/plugin-form-builder/types'

export const buildInitialFormState = (fields: FormFieldBlock[]) => {
  return fields.reduce((initialSchema, field) => {
    if (field.blockType === 'checkbox') {
      return {
        ...initialSchema,
        [field.name]: false,
      }
    }
    if (field.blockType === 'country') {
      return {
        ...initialSchema,
        [field.name]: '',
      }
    }
    if (field.blockType === 'email') {
      return {
        ...initialSchema,
        [field.name]: '',
      }
    }
    if (field.blockType === 'text') {
      return {
        ...initialSchema,
        [field.name]: '',
      }
    }
    if (field.blockType === 'select') {
      return {
        ...initialSchema,
        [field.name]: '',
      }
    }
    if (field.blockType === 'state') {
      return {
        ...initialSchema,
        [field.name]: '',
      }
    }
  }, {})
}
```

## File: src/components/Blocks/Form/fields.tsx
```typescript
import { Checkbox } from './Checkbox'
import { Country } from './Country'
import { Email } from './Email'
import { Message } from './Message'
import { Number } from './Number'
import { Select } from './Select'
import { State } from './State'
import { Text } from './Text'
import { Textarea } from './Textarea'

export const fields = {
  checkbox: Checkbox,
  country: Country,
  email: Email,
  message: Message,
  number: Number,
  select: Select,
  state: State,
  text: Text,
  textarea: Textarea,
}
```

## File: src/components/Blocks/Form/index.module.scss
```scss
@use '../.../../../../css/queries.scss' as *;

.form {
  display: flex;
  flex-direction: column;
}

.hasSubmitted {
  align-items: center;
  justify-content: center;
  height: 80vh;

  @include small-break {
    height: 100%;
  }
}

.intro {
  margin-bottom: var(--base);

  @include mid-break {
    margin-bottom: var(--base);
  }
}

.confirmationMessage {
  max-width: 800px;
  text-align: center;

  * {
    margin: 0;
  }
}

.fieldWrap {
  position: relative;
  z-index: 2;
  display: flex;
  flex-wrap: wrap;
  margin-left: calc(var(--base) * -0.5);
  margin-right: calc(var(--base) * -0.5);
  width: calc(100% + #{var(--base)});

  > * {
    width: 100%;
  }
}

.Select {
  & label {
    position: absolute;
    top: calc(var(--base) * -0.5);
  }
}

.sidebarContent {
  @include mid-break {
    display: none;
  }
}

.mobileSidebar {
  display: none;

  @include mid-break {
    display: block;
    margin-bottom: calc(var(--base) * 2);
  }
}
```

## File: src/components/Blocks/Form/index.tsx
```typescript
'use client'
import type { Form as FormType } from '@payloadcms/plugin-form-builder/types'

import { useRouter } from 'next/navigation'
import React, { useCallback, useState } from 'react'
import { useForm } from 'react-hook-form'

import { Button } from '../../Button'
import { Gutter } from '../../Gutter'
import RichText from '../../RichText'
import { buildInitialFormState } from './buildInitialFormState'
import { fields } from './fields'
import classes from './index.module.scss'

export type Value = unknown

export interface Property {
  [key: string]: Value
}

export interface Data {
  [key: string]: Property | Property[] | Value
}

export type FormBlockType = {
  blockName?: string
  blockType?: 'formBlock'
  enableIntro: boolean
  form: FormType
  introContent?: {
    [k: string]: unknown
  }[]
}

export const FormBlock: React.FC<
  FormBlockType & {
    id?: string
  }
> = (props) => {
  const {
    enableIntro,
    form: formFromProps,
    form: { id: formID, confirmationMessage, confirmationType, redirect, submitButtonLabel } = {},
    introContent,
  } = props

  const formMethods = useForm({
    defaultValues: buildInitialFormState(formFromProps.fields),
  })
  const {
    control,
    formState: { errors },
    getValues,
    handleSubmit,
    register,
    setValue,
  } = formMethods

  const [isLoading, setIsLoading] = useState(false)
  const [hasSubmitted, setHasSubmitted] = useState<boolean>()
  const [error, setError] = useState<{ message: string; status?: string } | undefined>()
  const router = useRouter()

  const onSubmit = useCallback(
    (data: Data) => {
      let loadingTimerID: ReturnType<typeof setTimeout>
      const submitForm = async () => {
        setError(undefined)

        const dataToSend = Object.entries(data).map(([name, value]) => ({
          field: name,
          value,
        }))

        // delay loading indicator by 1s
        loadingTimerID = setTimeout(() => {
          setIsLoading(true)
        }, 1000)

        try {
          const req = await fetch(`${process.env.NEXT_PUBLIC_PAYLOAD_URL}/api/form-submissions`, {
            body: JSON.stringify({
              form: formID,
              submissionData: dataToSend,
            }),
            headers: {
              'Content-Type': 'application/json',
            },
            method: 'POST',
          })

          const res = await req.json()

          clearTimeout(loadingTimerID)

          if (req.status >= 400) {
            setIsLoading(false)

            setError({
              message: res.errors?.[0]?.message || 'Internal Server Error',
              status: res.status,
            })

            return
          }

          setIsLoading(false)
          setHasSubmitted(true)

          if (confirmationType === 'redirect' && redirect) {
            const { url } = redirect

            const redirectUrl = url

            if (redirectUrl) router.push(redirectUrl)
          }
        } catch (err) {
          console.warn(err)
          setIsLoading(false)
          setError({
            message: 'Something went wrong.',
          })
        }
      }

      void submitForm()
    },
    [router, formID, redirect, confirmationType],
  )

  return (
    <Gutter>
      <div
        className={[classes.form, hasSubmitted && classes.hasSubmitted].filter(Boolean).join(' ')}
      >
        {enableIntro && introContent && !hasSubmitted && (
          <RichText className={classes.intro} content={introContent} />
        )}
        {!isLoading && hasSubmitted && confirmationType === 'message' && (
          <RichText className={classes.confirmationMessage} content={confirmationMessage} />
        )}
        {isLoading && !hasSubmitted && <p>Loading, please wait...</p>}
        {error && <div>{`${error.status || '500'}: ${error.message || ''}`}</div>}
        {!hasSubmitted && (
          <form id={formID} onSubmit={handleSubmit(onSubmit)}>
            <div className={classes.fieldWrap}>
              {formFromProps &&
                formFromProps.fields &&
                formFromProps.fields.map((field, index) => {
                  const Field: React.FC<any> = fields?.[field.blockType]
                  if (Field) {
                    return (
                      <React.Fragment key={index}>
                        <Field
                          form={formFromProps}
                          {...field}
                          {...formMethods}
                          control={control}
                          errors={errors}
                          register={register}
                        />
                      </React.Fragment>
                    )
                  }
                  return null
                })}
            </div>
            <Button appearance="primary" el="button" form={formID} label={submitButtonLabel} />
          </form>
        )}
      </div>
    </Gutter>
  )
}
```

## File: src/components/Blocks/Form/shared.scss
```scss
@use '../../../css/common.scss' as *;

@mixin formInput() {
  all: unset;
  -webkit-appearance: none;
  box-sizing: border-box;
  font-family: var(--font-body);
  width: 100%;
  border: 1px solid var(--color-black);
  background: var(--color-white);
  color: var(--color-black);
  font-size: 1rem;
  height: calc(var(--base) * 2.5);
  line-height: var(--base);
  padding: calc(var(--base) * 0.75);

  &::-moz-placeholder,
  &::-webkit-input-placeholder {
    color: var(--color-mid-gray);
    font-weight: normal;
    font-size: 1rem;
  }

  &:hover {
    border-color: var(--color-mid-gray);
  }

  &:focus,
  &:active {
    border-color: var(--color-gray);
    outline: 0;
  }

  &:disabled {
    background: var(--color-light-gray);
    color: var(--color-gray);

    &:hover {
      border-color: var(--color-light-gray);
    }
  }
}
```

## File: src/components/Blocks/index.tsx
```typescript
import React, { Fragment } from 'react'

import type { Page } from '../../payload-types'

import { toKebabCase } from '../../utilities/toKebabCase'
import { VerticalPadding } from '../VerticalPadding'
import { FormBlock } from './Form'

const blockComponents = {
  formBlock: FormBlock,
}

const Blocks: React.FC<{
  blocks: Page['layout']
}> = (props) => {
  const { blocks } = props

  const hasBlocks = blocks && Array.isArray(blocks) && blocks.length > 0

  if (hasBlocks) {
    return (
      <Fragment>
        {blocks.map((block, index) => {
          const { blockName, blockType, form } = block

          const isFormBlock = blockType === 'formBlock'
          // eslint-disable-next-line no-empty
          {
          }
          // @ts-expect-error: Form is not defined
          const formID: string = isFormBlock && form && (typeof form === 'string' ? form : form.id)

          if (blockType && blockType in blockComponents) {
            const Block = blockComponents[blockType]

            return (
              <VerticalPadding bottom="small" key={isFormBlock ? formID : index} top="small">
                {/*@ts-expect-error: Block type is dynamically determined */}
                <Block id={toKebabCase(blockName)} {...block} />
              </VerticalPadding>
            )
          }
          return null
        })}
      </Fragment>
    )
  }

  return null
}

export default Blocks
```

## File: src/components/Button/index.module.scss
```scss
@import '../../css/type.scss';

.content {
  display: flex;
  align-items: center;
  justify-content: center;

  svg {
    margin-right: calc(var(--base) / 2);
    width: var(--base);
    height: var(--base);
  }
}

.label {
  @extend %label;
  display: flex;
  align-items: center;
}

.button {
  all: unset;
  cursor: pointer;
  box-sizing: border-box;
  display: inline-flex;
  position: relative;
  text-decoration: none;
  padding: 12px 18px;
  margin-bottom: var(--base);
  align-items: center;
  justify-content: center;
}

.appearance--primary {
  background-color: var(--color-black);
  color: var(--color-white);

  &:hover,
  &:focus {
    background-color: var(--color-white);
    color: var(--color-black);
    box-shadow: inset 0 0 0 1px var(--color-black);
  }
}

.appearance--secondary {
  background-color: var(--color-white);
  box-shadow: inset 0 0 0 1px var(--color-black);

  &:hover,
  &:focus {
    background-color: var(--color-black);
    color: var(--color-white);
    box-shadow: inset 0 0 0 1px var(--color-black);
  }
}

.appearance--default {
  padding: 0;
  margin-left: -8px;
}
```

## File: src/components/Button/index.tsx
```typescript
import type { ElementType } from 'react'

import Link from 'next/link'
import React from 'react'

import classes from './index.module.scss'

export type Props = {
  appearance?: 'default' | 'primary' | 'secondary'
  className?: string
  el?: 'a' | 'button' | 'link'
  form?: string
  href?: string
  label?: string
  newTab?: boolean | null
  onClick?: () => void
}

const elements = {
  a: 'a',
  button: 'button',
  link: Link,
}

export const Button: React.FC<Props> = ({
  appearance,
  className: classNameFromProps,
  el = 'button',
  form,
  href,
  label,
  newTab,
}) => {
  const newTabProps = newTab ? { rel: 'noopener noreferrer', target: '_blank' } : {}
  const Element: ElementType = el
  const className = [classNameFromProps, classes[`appearance--${appearance}`], classes.button]
    .filter(Boolean)
    .join(' ')

  const elementProps = {
    ...newTabProps,
    className,
    form,
    href,
  }

  const content = (
    <div className={classes.content}>
      <span className={classes.label}>{label}</span>
    </div>
  )

  return (
    <Element {...elementProps}>
      <React.Fragment>
        {el === 'link' && (
          <Link {...newTabProps} className={elementProps.className} href={href || ''}>
            {content}
          </Link>
        )}
        {el !== 'link' && <React.Fragment>{content}</React.Fragment>}
      </React.Fragment>
    </Element>
  )
}
```

## File: src/components/CloseModalOnRouteChange/index.tsx
```typescript
'use client'
import type React from 'react'

import { useModal } from '@faceless-ui/modal'
import { usePathname } from 'next/navigation'
import { useEffect } from 'react'

export const CloseModalOnRouteChange: React.FC = () => {
  const { closeAllModals } = useModal()
  const pathname = usePathname()

  useEffect(() => {
    closeAllModals()
  }, [pathname, closeAllModals])

  return null
}
```

## File: src/components/Gutter/index.module.scss
```scss
.gutterLeft {
  padding-left: var(--gutter-h);
}

.gutterRight {
  padding-right: var(--gutter-h);
}
```

## File: src/components/Gutter/index.tsx
```typescript
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
      className={[left && classes.gutterLeft, right && classes.gutterRight, className]
        .filter(Boolean)
        .join(' ')}
      ref={ref}
    >
      {children}
    </div>
  )
}
```

## File: src/components/Header/index.module.scss
```scss
@use '../../css/queries.scss' as *;

.header {
  padding: var(--base) 0;
  z-index: var(--header-z-index);
}

.wrap {
  display: flex;
  justify-content: space-between;
}

.nav {
  a {
    text-decoration: none;
    margin-left: var(--base);
  }

  @include mid-break {
    display: none;
  }
}

.mobileMenuToggler {
  all: unset;
  cursor: pointer;
  display: none;

  &[aria-expanded='true'] {
    transform: rotate(-25deg);
  }

  @include mid-break {
    display: block;
  }
}
```

## File: src/components/Header/index.tsx
```typescript
import { ModalToggler } from '@faceless-ui/modal'
import Link from 'next/link'
import React from 'react'

import type { MainMenu } from '../../payload-types'

import { getCachedGlobal } from '../../utilities/getGlobals'
import { Gutter } from '../Gutter'
import { MenuIcon } from '../icons/Menu'
import { CMSLink } from '../Link'
import { Logo } from '../Logo'
import classes from './index.module.scss'
import { slug as menuModalSlug, MobileMenuModal } from './MobileMenuModal'

type HeaderBarProps = {
  children?: React.ReactNode
}

export const HeaderBar: React.FC<HeaderBarProps> = ({ children }) => {
  return (
    <header className={classes.header}>
      <Gutter className={classes.wrap}>
        <Link href="/">
          <Logo />
        </Link>

        {children}

        <ModalToggler className={classes.mobileMenuToggler} slug={menuModalSlug}>
          <MenuIcon />
        </ModalToggler>
      </Gutter>
    </header>
  )
}

export async function Header() {
  const header: MainMenu = await getCachedGlobal('main-menu', 1)()

  const navItems = header?.navItems || []

  return (
    <React.Fragment>
      <HeaderBar>
        <nav className={classes.nav}>
          {navItems.map(({ link }, i) => {
            return <CMSLink key={i} {...link} />
          })}
        </nav>
      </HeaderBar>

      <MobileMenuModal navItems={navItems} />
    </React.Fragment>
  )
}
```

## File: src/components/Header/mobileMenuModal.module.scss
```scss
@use '../../css/common.scss' as *;

.mobileMenuModal {
  position: relative;
  width: 100%;
  height: 100%;
  border: none;
  padding: 0;
  opacity: 1;
  display: none;

  @include mid-break {
    display: block;
  }
}

.contentContainer {
  padding: 20px;
}

.mobileMenuItems {
  display: flex;
  flex-direction: column;
  height: 100%;
  margin-top: 30px;
}

.menuItem {
  @extend %h4;
  margin-top: 0;
}
```

## File: src/components/Header/MobileMenuModal.tsx
```typescript
import { Modal } from '@faceless-ui/modal'
import React from 'react'

import type { MainMenu } from '../../payload-types'

import { HeaderBar } from '.'
import { Gutter } from '../Gutter'
import { CMSLink } from '../Link'
import classes from './mobileMenuModal.module.scss'

type Props = {
  navItems: MainMenu['navItems']
}

export const slug = 'menu-modal'

export const MobileMenuModal: React.FC<Props> = ({ navItems }) => {
  return (
    <Modal className={classes.mobileMenuModal} slug={slug}>
      <HeaderBar />

      <Gutter>
        <div className={classes.mobileMenuItems}>
          {navItems &&
            navItems.map(({ link }, i) => {
              return <CMSLink className={classes.menuItem} key={i} {...link} />
            })}
        </div>
      </Gutter>
    </Modal>
  )
}
```

## File: src/components/icons/Check/index.module.scss
```scss
.checkBox {
  height: var(--base);
  width: var(--base);

  .stroke {
    stroke-width: 1px;
    fill: none;
    stroke: currentColor;
  }

  &:local() {
    .stroke {
      stroke-width: 2px;
    }
  }
}
```

## File: src/components/icons/Check/index.tsx
```typescript
import React from 'react'

import classes from './index.module.scss'

export const Check: React.FC = () => {
  return (
    <svg className={classes.checkBox} height="100%" viewBox="0 0 25 25" width="100%">
      <path
        className={classes.stroke}
        d="M10.6092 16.0192L17.6477 8.98076"
        strokeLinecap="square"
        strokeLinejoin="bevel"
      />
      <path
        className={classes.stroke}
        d="M7.35229 12.7623L10.6092 16.0192"
        strokeLinecap="square"
        strokeLinejoin="bevel"
      />
    </svg>
  )
}
```

## File: src/components/icons/Menu/index.tsx
```typescript
import React from 'react'

export const MenuIcon: React.FC = () => {
  return (
    <svg fill="none" height="25" viewBox="0 0 25 25" width="25" xmlns="http://www.w3.org/2000/svg">
      <rect fill="currentColor" height="2" width="18" x="3.5" y="4.5" />
      <rect fill="currentColor" height="2" width="18" x="3.5" y="11.5" />
      <rect fill="currentColor" height="2" width="18" x="3.5" y="18.5" />
    </svg>
  )
}
```

## File: src/components/Link/index.tsx
```typescript
import Link from 'next/link'
import React from 'react'

import type { Page } from '../../payload-types'

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
```

## File: src/components/Logo/index.tsx
```typescript
import React from 'react'

export const Logo: React.FC = () => {
  return (
    <svg
      fill="none"
      height="29"
      viewBox="0 0 123 29"
      width="123"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path
        d="M34.7441 22.9997H37.2741V16.3297H41.5981C44.7031 16.3297 46.9801 14.9037 46.9801 11.4537C46.9801 8.00369 44.7031 6.55469 41.5981 6.55469H34.7441V22.9997ZM37.2741 14.1447V8.73969H41.4831C43.3921 8.73969 44.3581 9.59069 44.3581 11.4537C44.3581 13.2937 43.3921 14.1447 41.4831 14.1447H37.2741Z"
        fill="black"
      />
      <path
        d="M51.3652 23.3217C53.2742 23.3217 54.6082 22.5627 55.3672 21.3437H55.4132C55.5512 22.6777 56.1492 23.1147 57.2762 23.1147C57.6442 23.1147 58.0352 23.0687 58.4262 22.9767V21.5967C58.2882 21.6197 58.2192 21.6197 58.1502 21.6197C57.7132 21.6197 57.5982 21.1827 57.5982 20.3317V14.9497C57.5982 11.9137 55.6662 10.9017 53.2512 10.9017C49.6632 10.9017 48.1912 12.6727 48.0762 14.9267H50.3762C50.4912 13.3627 51.1122 12.7187 53.1592 12.7187C54.8842 12.7187 55.3902 13.4317 55.3902 14.2827C55.3902 15.4327 54.2632 15.6627 52.4232 16.0077C49.5022 16.5597 47.5242 17.3417 47.5242 19.9637C47.5242 21.9647 49.0192 23.3217 51.3652 23.3217ZM49.8702 19.8027C49.8702 18.5837 50.7442 18.0087 52.8142 17.5947C54.0102 17.3417 55.0222 17.0887 55.3902 16.7437V18.4227C55.3902 20.4697 53.8952 21.5047 51.8712 21.5047C50.4682 21.5047 49.8702 20.9067 49.8702 19.8027Z"
        fill="black"
      />
      <path
        d="M61.4996 27.1167C63.3166 27.1167 64.4436 26.1737 65.5706 23.2757L70.2166 11.2697H67.8476L64.6276 20.2397H64.5816L61.1546 11.2697H58.6936L63.4316 22.8847C62.9716 24.7247 61.9136 25.1847 61.0166 25.1847C60.6486 25.1847 60.4416 25.1617 60.0506 25.1157V26.9557C60.6486 27.0707 60.9936 27.1167 61.4996 27.1167Z"
        fill="black"
      />
      <path d="M71.5939 22.9997H73.8479V6.55469H71.5939V22.9997Z" fill="black" />
      <path
        d="M81.6221 23.3447C85.2791 23.3447 87.4871 20.7917 87.4871 17.1117C87.4871 13.4547 85.2791 10.9017 81.6451 10.9017C77.9651 10.9017 75.7571 13.4777 75.7571 17.1347C75.7571 20.8147 77.9651 23.3447 81.6221 23.3447ZM78.1031 17.1347C78.1031 14.6737 79.2071 12.7877 81.6451 12.7877C84.0371 12.7877 85.1411 14.6737 85.1411 17.1347C85.1411 19.5727 84.0371 21.4817 81.6451 21.4817C79.2071 21.4817 78.1031 19.5727 78.1031 17.1347Z"
        fill="black"
      />
      <path
        d="M92.6484 23.3217C94.5574 23.3217 95.8914 22.5627 96.6504 21.3437H96.6964C96.8344 22.6777 97.4324 23.1147 98.5594 23.1147C98.9274 23.1147 99.3184 23.0687 99.7094 22.9767V21.5967C99.5714 21.6197 99.5024 21.6197 99.4334 21.6197C98.9964 21.6197 98.8814 21.1827 98.8814 20.3317V14.9497C98.8814 11.9137 96.9494 10.9017 94.5344 10.9017C90.9464 10.9017 89.4744 12.6727 89.3594 14.9267H91.6594C91.7744 13.3627 92.3954 12.7187 94.4424 12.7187C96.1674 12.7187 96.6734 13.4317 96.6734 14.2827C96.6734 15.4327 95.5464 15.6627 93.7064 16.0077C90.7854 16.5597 88.8074 17.3417 88.8074 19.9637C88.8074 21.9647 90.3024 23.3217 92.6484 23.3217ZM91.1534 19.8027C91.1534 18.5837 92.0274 18.0087 94.0974 17.5947C95.2934 17.3417 96.3054 17.0887 96.6734 16.7437V18.4227C96.6734 20.4697 95.1784 21.5047 93.1544 21.5047C91.7514 21.5047 91.1534 20.9067 91.1534 19.8027Z"
        fill="black"
      />
      <path
        d="M106.181 23.3217C108.021 23.3217 109.148 22.4477 109.792 21.6197H109.838V22.9997H112.092V6.55469H109.838V12.6957H109.792C109.148 11.7757 108.021 10.9247 106.181 10.9247C103.191 10.9247 100.914 13.2707 100.914 17.1347C100.914 20.9987 103.191 23.3217 106.181 23.3217ZM103.26 17.1347C103.26 14.8347 104.341 12.8107 106.549 12.8107C108.573 12.8107 109.815 14.4667 109.815 17.1347C109.815 19.7797 108.573 21.4587 106.549 21.4587C104.341 21.4587 103.26 19.4347 103.26 17.1347Z"
        fill="black"
      />
      <path
        d="M12.2464 2.33838L22.2871 8.83812V21.1752L14.7265 25.8854V13.5484L4.67383 7.05725L12.2464 2.33838Z"
        fill="black"
      />
      <path d="M11.477 25.2017V15.5747L3.90039 20.2936L11.477 25.2017Z" fill="black" />
      <path
        d="M120.442 6.30273C119.086 6.30273 117.998 7.29978 117.998 8.75952C117.998 10.2062 119.086 11.1968 120.442 11.1968C121.791 11.1968 122.879 10.2062 122.879 8.75952C122.879 7.29978 121.791 6.30273 120.442 6.30273ZM120.442 10.7601C119.34 10.7601 118.48 9.95207 118.48 8.75952C118.48 7.54742 119.34 6.73935 120.442 6.73935C121.563 6.73935 122.397 7.54742 122.397 8.75952C122.397 9.95207 121.563 10.7601 120.442 10.7601ZM120.52 8.97457L121.048 9.9651H121.641L121.041 8.86378C121.367 8.72042 121.511 8.45975 121.511 8.17302C121.511 7.49528 121.054 7.36495 120.285 7.36495H119.49V9.9651H120.025V8.97457H120.52ZM120.37 7.78853C120.729 7.78853 120.976 7.86673 120.976 8.17953C120.976 8.43368 120.807 8.56402 120.403 8.56402H120.025V7.78853H120.37Z"
        fill="black"
      />
    </svg>
  )
}
```

## File: src/components/RichText/index.tsx
```typescript
import React from 'react'

import { serializeLexical } from './serialize'

const RichText: React.FC<{ className?: string; content: any; enableGutter?: boolean }> = ({
  className,
  content,
}) => {
  if (!content) {
    return null
  }

  return (
    <div className={[className].filter(Boolean).join(' ')}>
      {content &&
        !Array.isArray(content) &&
        typeof content === 'object' &&
        'root' in content &&
        serializeLexical({ nodes: content?.root?.children })}
    </div>
  )
}

export default RichText
```

## File: src/components/RichText/nodeFormat.tsx
```typescript
// @ts-nocheck
//This copy-and-pasted from lexical here here: https://github.com/facebook/lexical/blob/c2ceee223f46543d12c574e62155e619f9a18a5d/packages/lexical/src/LexicalConstants.ts

import type { ElementFormatType, TextFormatType } from '@payloadcms/richtext-lexical/lexical'
import type { TextDetailType, TextModeType } from 'lexical/nodes/LexicalTextNode'

/**
 * Copyright (c) Meta Platforms, Inc. and affiliates.
 *
 * This source code is licensed under the MIT license found in the
 * LICENSE file in the root directory of this source tree.
 *
 */

// DOM
export const DOM_ELEMENT_TYPE = 1
export const DOM_TEXT_TYPE = 3

// Reconciling
export const NO_DIRTY_NODES = 0
export const HAS_DIRTY_NODES = 1
export const FULL_RECONCILE = 2

// Text node modes
export const IS_NORMAL = 0
export const IS_TOKEN = 1
export const IS_SEGMENTED = 2
// IS_INERT = 3

// Text node formatting
export const IS_BOLD = 1
export const IS_ITALIC = 1 << 1
export const IS_STRIKETHROUGH = 1 << 2
export const IS_UNDERLINE = 1 << 3
export const IS_CODE = 1 << 4
export const IS_SUBSCRIPT = 1 << 5
export const IS_SUPERSCRIPT = 1 << 6
export const IS_HIGHLIGHT = 1 << 7

export const IS_ALL_FORMATTING =
  IS_BOLD |
  IS_ITALIC |
  IS_STRIKETHROUGH |
  IS_UNDERLINE |
  IS_CODE |
  IS_SUBSCRIPT |
  IS_SUPERSCRIPT |
  IS_HIGHLIGHT

// Text node details
export const IS_DIRECTIONLESS = 1
export const IS_UNMERGEABLE = 1 << 1

// Element node formatting
export const IS_ALIGN_LEFT = 1
export const IS_ALIGN_CENTER = 2
export const IS_ALIGN_RIGHT = 3
export const IS_ALIGN_JUSTIFY = 4
export const IS_ALIGN_START = 5
export const IS_ALIGN_END = 6

// Reconciliation
export const NON_BREAKING_SPACE = '\u00A0'
const ZERO_WIDTH_SPACE = '\u200b'

export const DOUBLE_LINE_BREAK = '\n\n'

// For FF, we need to use a non-breaking space, or it gets composition
// in a stuck state.

const RTL = '\u0591-\u07FF\uFB1D-\uFDFD\uFE70-\uFEFC'
const LTR =
  'A-Za-z\u00C0-\u00D6\u00D8-\u00F6' +
  '\u00F8-\u02B8\u0300-\u0590\u0800-\u1FFF\u200E\u2C00-\uFB1C' +
  '\uFE00-\uFE6F\uFEFD-\uFFFF'

// eslint-disable-next-line
export const RTL_REGEX = new RegExp('^[^' + LTR + ']*[' + RTL + ']')
// eslint-disable-next-line
export const LTR_REGEX = new RegExp('^[^' + RTL + ']*[' + LTR + ']')

export const TEXT_TYPE_TO_FORMAT: Record<string | TextFormatType, number> = {
  bold: IS_BOLD,
  code: IS_CODE,
  highlight: IS_HIGHLIGHT,
  italic: IS_ITALIC,
  strikethrough: IS_STRIKETHROUGH,
  subscript: IS_SUBSCRIPT,
  superscript: IS_SUPERSCRIPT,
  underline: IS_UNDERLINE,
}

export const DETAIL_TYPE_TO_DETAIL: Record<string | TextDetailType, number> = {
  directionless: IS_DIRECTIONLESS,
  unmergeable: IS_UNMERGEABLE,
}

export const ELEMENT_TYPE_TO_FORMAT: Record<Exclude<ElementFormatType, ''>, number> = {
  center: IS_ALIGN_CENTER,
  end: IS_ALIGN_END,
  justify: IS_ALIGN_JUSTIFY,
  left: IS_ALIGN_LEFT,
  right: IS_ALIGN_RIGHT,
  start: IS_ALIGN_START,
}

export const ELEMENT_FORMAT_TO_TYPE: Record<number, ElementFormatType> = {
  [IS_ALIGN_CENTER]: 'center',
  [IS_ALIGN_END]: 'end',
  [IS_ALIGN_JUSTIFY]: 'justify',
  [IS_ALIGN_LEFT]: 'left',
  [IS_ALIGN_RIGHT]: 'right',
  [IS_ALIGN_START]: 'start',
}

export const TEXT_MODE_TO_TYPE: Record<TextModeType, 0 | 1 | 2> = {
  normal: IS_NORMAL,
  segmented: IS_SEGMENTED,
  token: IS_TOKEN,
}

export const TEXT_TYPE_TO_MODE: Record<number, TextModeType> = {
  [IS_NORMAL]: 'normal',
  [IS_SEGMENTED]: 'segmented',
  [IS_TOKEN]: 'token',
}
```

## File: src/components/RichText/serialize.tsx
```typescript
import type { LinkFields, SerializedLinkNode } from '@payloadcms/richtext-lexical'
import type {
  SerializedElementNode,
  SerializedLexicalNode,
  SerializedTextNode,
} from '@payloadcms/richtext-lexical/lexical'
import type {
  SerializedListItemNode,
  SerializedListNode,
} from '@payloadcms/richtext-lexical/lexical/list'
import type { SerializedHeadingNode } from '@payloadcms/richtext-lexical/lexical/rich-text'
import type { JSX } from 'react'

import React, { Fragment } from 'react'

import { CMSLink } from '../Link'
import {
  IS_BOLD,
  IS_CODE,
  IS_ITALIC,
  IS_STRIKETHROUGH,
  IS_SUBSCRIPT,
  IS_SUPERSCRIPT,
  IS_UNDERLINE,
} from './nodeFormat'

interface Props {
  nodes: SerializedLexicalNode[]
}

export function serializeLexical({ nodes }: Props): JSX.Element {
  return (
    <Fragment>
      {nodes?.map((_node, index): JSX.Element | null => {
        if (_node.type === 'text') {
          const node = _node as SerializedTextNode
          let text = <React.Fragment key={index}>{node.text}</React.Fragment>
          if (node.format & IS_BOLD) {
            text = <strong key={index}>{text}</strong>
          }
          if (node.format & IS_ITALIC) {
            text = <em key={index}>{text}</em>
          }
          if (node.format & IS_STRIKETHROUGH) {
            text = (
              <span key={index} style={{ textDecoration: 'line-through' }}>
                {text}
              </span>
            )
          }
          if (node.format & IS_UNDERLINE) {
            text = (
              <span key={index} style={{ textDecoration: 'underline' }}>
                {text}
              </span>
            )
          }
          if (node.format & IS_CODE) {
            text = <code key={index}>{node.text}</code>
          }
          if (node.format & IS_SUBSCRIPT) {
            text = <sub key={index}>{text}</sub>
          }
          if (node.format & IS_SUPERSCRIPT) {
            text = <sup key={index}>{text}</sup>
          }

          return text
        }

        if (_node == null) {
          return null
        }

        // NOTE: Hacky fix for
        // https://github.com/facebook/lexical/blob/d10c4e6e55261b2fdd7d1845aed46151d0f06a8c/packages/lexical-list/src/LexicalListItemNode.ts#L133
        // which does not return checked: false (only true - i.e. there is no prop for false)
        const serializedChildrenFn = (node: SerializedElementNode): JSX.Element | null => {
          if (node.children == null) {
            return null
          } else {
            if (node?.type === 'list' && (node as SerializedListNode)?.listType === 'check') {
              for (const item of node.children) {
                if ('checked' in item) {
                  if (!item?.checked) {
                    item.checked = false
                  }
                }
              }
              return serializeLexical({ nodes: node.children })
            } else {
              return serializeLexical({ nodes: node.children })
            }
          }
        }

        const serializedChildren =
          'children' in _node ? serializedChildrenFn(_node as SerializedElementNode) : ''

        switch (_node.type) {
          case 'heading': {
            const node = _node as SerializedHeadingNode

            type Heading = Extract<keyof JSX.IntrinsicElements, 'h1' | 'h2' | 'h3' | 'h4' | 'h5'>
            const Tag = node?.tag as Heading
            return <Tag key={index}>{serializedChildren}</Tag>
          }
          case 'linebreak': {
            return <br key={index} />
          }
          case 'link': {
            const node = _node as SerializedLinkNode

            const fields: LinkFields = node.fields

            return (
              <CMSLink
                key={index}
                newTab={Boolean(fields?.newTab)}
                reference={fields.doc as any}
                type={fields.linkType === 'internal' ? 'reference' : 'custom'}
                url={fields.url}
              >
                {serializedChildren}
              </CMSLink>
            )
          }
          case 'list': {
            const node = _node as SerializedListNode

            type List = Extract<keyof JSX.IntrinsicElements, 'ol' | 'ul'>
            const Tag = node?.tag as List
            return (
              <Tag className="list" key={index}>
                {serializedChildren}
              </Tag>
            )
          }
          case 'listitem': {
            const node = _node as SerializedListItemNode

            if (node?.checked != null) {
              return (
                <li
                  aria-checked={node.checked ? 'true' : 'false'}
                  className={` ${node.checked ? '' : ''}`}
                  key={index}
                  // eslint-disable-next-line jsx-a11y/no-noninteractive-element-to-interactive-role
                  role="checkbox"
                  tabIndex={-1}
                  value={node?.value}
                >
                  {serializedChildren}
                </li>
              )
            } else {
              return (
                <li key={index} value={node?.value}>
                  {serializedChildren}
                </li>
              )
            }
          }
          case 'paragraph': {
            return <p key={index}>{serializedChildren}</p>
          }
          case 'quote': {
            return <blockquote key={index}>{serializedChildren}</blockquote>
          }

          default:
            return null
        }
      })}
    </Fragment>
  )
}
```

## File: src/components/VerticalPadding/index.module.scss
```scss
.top-large {
  padding-top: var(--block-padding);
}

.top-medium {
  padding-top: calc(var(--block-padding) / 2);
}

.top-small {
  padding-top: calc(var(--block-padding) / 3);
}

.bottom-large {
  padding-bottom: var(--block-padding);
}

.bottom-medium {
  padding-bottom: calc(var(--block-padding) / 2);
}

.bottom-small {
  padding-bottom: calc(var(--block-padding) / 3);
}
```

## File: src/components/VerticalPadding/index.tsx
```typescript
import React from 'react'

import classes from './index.module.scss'

export type VerticalPaddingOptions = 'large' | 'medium' | 'none' | 'small'

type Props = {
  bottom?: VerticalPaddingOptions
  children: React.ReactNode
  className?: string
  top?: VerticalPaddingOptions
}

export const VerticalPadding: React.FC<Props> = ({
  bottom = 'medium',
  children,
  className,
  top = 'medium',
}) => {
  return (
    <div
      className={[className, classes[`top-${top}`], classes[`bottom-${bottom}`]]
        .filter(Boolean)
        .join(' ')}
    >
      {children}
    </div>
  )
}
```

## File: src/css/app.scss
```scss
@use './queries.scss' as *;
@use './colors.scss' as *;
@use './type.scss' as *;

:root {
  --breakpoint-xs-width: #{$breakpoint-xs-width};
  --breakpoint-s-width: #{$breakpoint-s-width};
  --breakpoint-m-width: #{$breakpoint-m-width};
  --breakpoint-l-width: #{$breakpoint-l-width};
  --scrollbar-width: 17px;

  --base: 24px;
  --font-body: system-ui;
  --font-mono: 'Roboto Mono', monospace;

  --gutter-h: 180px;
  --block-padding: 120px;

  --header-z-index: 100;
  --modal-z-index: 90;

  @include large-break {
    --gutter-h: 144px;
    --block-padding: 96px;
  }

  @include mid-break {
    --gutter-h: 24px;
    --block-padding: 60px;
  }
}

/////////////////////////////
// GLOBAL STYLES
/////////////////////////////

* {
  box-sizing: border-box;
}

html {
  @extend %body;
  background: var(--color-white);
  -webkit-font-smoothing: antialiased;
}

html,
body,
#app {
  height: 100%;
}

body {
  font-family: var(--font-body);
  color: var(--color-black);
  margin: 0;
}

::selection {
  background: var(--color-green);
  color: var(--color-black);
}

::-moz-selection {
  background: var(--color-green);
  color: var(--color-black);
}

img {
  max-width: 100%;
  height: auto;
  display: block;
}

h1 {
  @extend %h1;
}

h2 {
  @extend %h2;
}

h3 {
  @extend %h3;
}

h4 {
  @extend %h4;
}

h5 {
  @extend %h5;
}

h6 {
  @extend %h6;
}

p {
  margin: var(--base) 0;

  @include mid-break {
    margin: calc(var(--base) * 0.75) 0;
  }
}

ul,
ol {
  padding-left: var(--base);
  margin: 0 0 var(--base);
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
```

## File: src/css/colors.scss
```scss
:root {
  --color-red: rgb(255, 0, 0);
  --color-green: rgb(178, 255, 214);
  --color-white: rgb(255, 255, 255);
  --color-dark-gray: rgb(51, 52, 52);
  --color-mid-gray: rgb(196, 196, 196);
  --color-gray: rgb(212, 212, 212);
  --color-light-gray: rgb(244, 244, 244);
  --color-black: rgb(0, 0, 0);
}
```

## File: src/css/common.scss
```scss
@forward './queries.scss';
@forward './type.scss';
```

## File: src/css/queries.scss
```scss
$breakpoint-xs-width: 400px;
$breakpoint-s-width: 768px;
$breakpoint-m-width: 1024px;
$breakpoint-l-width: 1440px;

////////////////////////////
// MEDIA QUERIES
/////////////////////////////

@mixin extra-small-break {
  @media (max-width: #{$breakpoint-xs-width}) {
    @content;
  }
}

@mixin small-break {
  @media (max-width: #{$breakpoint-s-width}) {
    @content;
  }
}

@mixin mid-break {
  @media (max-width: #{$breakpoint-m-width}) {
    @content;
  }
}

@mixin large-break {
  @media (max-width: #{$breakpoint-l-width}) {
    @content;
  }
}
```

## File: src/css/type.scss
```scss
@use 'queries' as *;

/////////////////////////////
// HEADINGS
/////////////////////////////

%h1,
%h2,
%h3,
%h4,
%h5,
%h6 {
  font-weight: 700;
}

%h1 {
  margin: 50px 0;
  font-size: 84px;
  line-height: 1;

  @include mid-break {
    font-size: 70px;
  }

  @include small-break {
    margin: 24px 0;
    font-size: 36px;
    line-height: 42px;
  }
}

%h2 {
  margin: 32px 0;
  font-size: 56px;
  line-height: 1;

  @include mid-break {
    margin: 36px 0;
    font-size: 48px;
  }

  @include small-break {
    margin: 24px 0;
    font-size: 28px;
    line-height: 32px;
  }
}

%h3 {
  margin: 28px 0;
  font-size: 48px;
  line-height: 56px;

  @include mid-break {
    font-size: 40px;
    line-height: 48px;
  }

  @include small-break {
    margin: 24px 0;
    font-size: 24px;
    line-height: 30px;
  }
}

%h4 {
  margin: 24px 0;
  font-size: 40px;
  line-height: 48px;

  @include mid-break {
    font-size: 33px;
    line-height: 36px;
  }

  @include small-break {
    margin: 20px 0;
    font-size: 20px;
    line-height: 24px;
  }
}

%h5 {
  margin: 20px 0;
  font-size: 32px;
  line-height: 42px;

  @include mid-break {
    font-size: 26px;
    line-height: 32px;
  }

  @include small-break {
    margin: 16px 0;
    font-size: 18px;
    line-height: 24px;
  }
}

%h6 {
  margin: 20px 0;
  font-size: 24px;
  line-height: 28px;

  @include mid-break {
    font-size: 20px;
    line-height: 30px;
  }

  @include small-break {
    margin: 16px 0;
    font-size: 16px;
    line-height: 22px;
  }
}

/////////////////////////////
// TYPE STYLES
/////////////////////////////

%body {
  font-size: 18px;
  line-height: 32px;

  @include mid-break {
    font-size: 15px;
    line-height: 24px;
  }

  @include small-break {
    font-size: 13px;
    line-height: 24px;
  }
}

%large-body {
  font-size: 25px;
  line-height: 32px;

  @include mid-break {
    font-size: 22px;
    line-height: 30px;
  }

  @include small-break {
    font-size: 17px;
    line-height: 24px;
  }
}

%label {
  font-size: 16px;
  line-height: 24px;
  letter-spacing: 3px;
  text-transform: uppercase;

  @include mid-break {
    font-size: 13px;
    letter-spacing: 2.75px;
  }

  @include small-break {
    font-size: 12px;
    line-height: 18px;
    letter-spacing: 2.625px;
  }
}
```

## File: src/fields/link.ts
```typescript
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
    linkTypes[0].admin!.width = '50%'
    linkTypes[1].admin!.width = '50%'

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
```

## File: src/fields/slug.ts
```typescript
import type { Field } from 'payload'

import deepMerge from '../utilities/deepMerge'
import { formatSlug } from '../utilities/formatSlug'

type Slug = (fieldToUse?: string, overrides?: Partial<Field>) => Field

export const slugField: Slug = (fieldToUse = 'title', overrides = {}) =>
  deepMerge<Field, Partial<Field>>(
    {
      name: 'slug',
      type: 'text',
      admin: {
        position: 'sidebar',
      },
      hooks: {
        beforeValidate: [formatSlug(fieldToUse)],
      },
      index: true,
      label: 'Slug',
    },
    overrides,
  )
```

## File: src/globals/MainMenu.ts
```typescript
import type { GlobalConfig } from 'payload'

import link from '../fields/link'

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
}
```

## File: src/migrations/seed.ts
```typescript
import type { MigrateUpArgs } from '@payloadcms/db-mongodb'

import { home } from '../seed/home'

import { advanced } from '../seed/advanced'
import { advancedForm } from '../seed/advancedForm'
import { basicForm } from '../seed/basicForm'
import { contact } from '../seed/contact'
import { contactForm } from '../seed/contactForm'
import { signUp } from '../seed/signUp'
import { signUpForm } from '../seed/signUpForm'

export async function up({ payload }: MigrateUpArgs): Promise<void> {
  await payload.create({
    collection: 'users',
    data: {
      email: 'demo@payloadcms.com',
      password: 'demo',
    },
  })

  const basicFormJSON = JSON.parse(JSON.stringify(basicForm))

  const { id: basicFormID } = await payload.create({
    collection: 'forms',
    data: basicFormJSON,
  })

  const contactFormJSON = JSON.parse(JSON.stringify(contactForm))

  const { id: contactFormID } = await payload.create({
    collection: 'forms',
    data: contactFormJSON,
  })

  const advancedFormJSON = JSON.parse(JSON.stringify(advancedForm))

  const { id: advancedFormID } = await payload.create({
    collection: 'forms',
    data: advancedFormJSON,
  })

  const signUpFormJSON = JSON.parse(JSON.stringify(signUpForm))

  const { id: signUpFormID } = await payload.create({
    collection: 'forms',
    data: signUpFormJSON,
  })

  const homePageJSON = JSON.parse(
    JSON.stringify(home).replace(/\{\{BASIC_FORM_ID\}\}/g, basicFormID.toString()),
  )

  const contactPageJSON = JSON.parse(
    JSON.stringify(contact).replace(/\{\{CONTACT_FORM_ID\}\}/g, contactFormID.toString()),
  )

  const advancedPageJSON = JSON.parse(
    JSON.stringify(advanced).replace(/\{\{ADVANCED_FORM_ID\}\}/g, advancedFormID.toString()),
  )

  const signupPageJSON = JSON.parse(
    JSON.stringify(signUp).replace(/\{\{SIGNUP_FORM_ID\}\}/g, signUpFormID.toString()),
  )

  await payload.create({
    collection: 'pages',
    data: homePageJSON,
  })

  const { id: contactPageID } = await payload.create({
    collection: 'pages',
    data: contactPageJSON,
  })

  const { id: advancedPageID } = await payload.create({
    collection: 'pages',
    data: advancedPageJSON,
  })

  const { id: signupPageID } = await payload.create({
    collection: 'pages',
    data: signupPageJSON,
  })

  await payload.updateGlobal({
    slug: 'main-menu',
    data: {
      navItems: [
        {
          link: {
            type: 'reference',
            label: 'Contact Form',
            reference: {
              relationTo: 'pages',
              value: contactPageID,
            },
          },
        },
        {
          link: {
            type: 'reference',
            label: 'Advanced Form',
            reference: {
              relationTo: 'pages',
              value: advancedPageID,
            },
          },
        },
        {
          link: {
            type: 'reference',
            label: 'Signup Form',
            reference: {
              relationTo: 'pages',
              value: signupPageID,
            },
          },
        },
      ],
    },
  })
}
```

## File: src/seed/advanced.ts
```typescript
export const advanced = {
  slug: 'advanced',
  _status: 'published',
  layout: [
    {
      id: '63adc92568224b995af9df14',
      blockType: 'formBlock',
      enableIntro: true,
      form: '{{ADVANCED_FORM_ID}}',
      introContent: {
        root: {
          type: 'root',
          children: [
            {
              type: 'heading',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Example advanced form:',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              tag: 'h3',
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          version: 1,
        },
      },
    },
  ],
  title: 'Advanced',
}
```

## File: src/seed/advancedForm.ts
```typescript
export const advancedForm = {
  id: '63c0835134d40cef85cc11a2',
  confirmationMessage: {
    root: {
      type: 'root',
      children: [
        {
          type: 'heading',
          children: [
            {
              type: 'text',
              detail: 0,
              format: 0,
              mode: 'normal',
              style: '',
              text: 'Your shipping information submission was successful.',
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          tag: 'h2',
          version: 1,
        },
      ],
      direction: 'ltr',
      format: '',
      indent: 0,
      version: 1,
    },
  },
  confirmationType: 'message',
  createdAt: '2023-01-12T22:01:53.023Z',
  emails: [
    {
      id: '6644edb9cffd2c6c48a44730',
      emailFrom: '"Payload" \u003Cdemo@payloadcms.com\u003E',
      emailTo: '{{email}}',
      message: {
        root: {
          type: 'root',
          children: [
            {
              type: 'paragraph',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Your example shipping information form submission was received successfully.',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              textFormat: 0,
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          version: 1,
        },
      },
      subject: "You've received a new message.",
    },
  ],
  fields: [
    {
      id: '63c081b169853127a8895312',
      name: 'first-name',
      blockName: 'first-name',
      blockType: 'text',
      label: 'First Name',
      required: true,
      width: 50,
    },
    {
      id: '63c081c669853127a8895313',
      name: 'last-name',
      blockName: 'last-name',
      blockType: 'text',
      label: 'Last Name',
      required: true,
      width: 50,
    },
    {
      id: '63c081e869853127a8895314',
      name: 'email',
      blockName: 'email',
      blockType: 'email',
      label: 'Email',
      required: true,
      width: 100,
    },
    {
      id: '63c081fe69853127a8895315',
      name: 'street-address',
      blockName: 'street-address',
      blockType: 'text',
      label: 'Street Address',
      required: true,
      width: 100,
    },
    {
      id: '63c0823169853127a8895316',
      name: 'street-address-two',
      blockName: 'street-address-two',
      blockType: 'text',
      label: 'Street Address Line 2',
      width: 100,
    },
    {
      id: '63c0825a69853127a8895317',
      name: 'city',
      blockName: 'city',
      blockType: 'text',
      label: 'City',
      required: true,
      width: 50,
    },
    {
      id: '63c0826569853127a8895318',
      name: 'state',
      blockName: 'state',
      blockType: 'state',
      label: 'State',
      required: true,
      width: 50,
    },
    {
      id: '63c082bb69853127a889531a',
      name: 'zip-code',
      blockName: 'zip-code',
      blockType: 'number',
      label: 'Postal / Zip Code',
      required: true,
      width: 50,
    },
    {
      id: '63c0829269853127a8895319',
      name: 'country',
      blockName: 'country',
      blockType: 'country',
      label: 'Country',
      required: true,
      width: 50,
    },
  ],
  redirect: {},
  submitButtonLabel: 'Submit',
  title: 'Advanced Form',
  updatedAt: '2023-01-12T22:01:53.023Z',
}
```

## File: src/seed/basicForm.ts
```typescript
export const basicForm = {
  id: '63c0651b132c8e2783f8dcae',
  confirmationMessage: {
    root: {
      type: 'root',
      children: [
        {
          type: 'heading',
          children: [
            {
              type: 'text',
              detail: 0,
              format: 0,
              mode: 'normal',
              style: '',
              text: 'The basic form has been submitted successfully.',
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          tag: 'h2',
          version: 1,
        },
      ],
      direction: 'ltr',
      format: '',
      indent: 0,
      version: 1,
    },
  },
  confirmationType: 'message',
  createdAt: '2022-12-28T20:48:53.181Z',
  emails: [
    {
      id: '6644edb9cffd2c6c48a44730',
      emailFrom: '"Payload" \u003Cdemo@payloadcms.com\u003E',
      emailTo: '{{email}}',
      message: {
        root: {
          type: 'root',
          children: [
            {
              type: 'paragraph',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Your basic form submission was successfully received.',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              textFormat: 0,
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          version: 1,
        },
      },
      subject: "You've received a new message.",
    },
  ],
  fields: [
    {
      id: '63adaaba5236fe69ca8973f8',
      name: 'first-name',
      blockName: 'first-name',
      blockType: 'text',
      label: 'First name',
      required: true,
      width: 50,
    },
    {
      id: '63bf4b1fd69cef4f34272f9a',
      name: 'last-name',
      blockName: 'last-name',
      blockType: 'text',
      label: 'Last name',
      required: true,
      width: 50,
    },
    {
      id: '63c0953adc1cd2c2f6c2d30b',
      name: 'email',
      blockName: 'email',
      blockType: 'email',
      label: 'Email',
      required: true,
      width: 100,
    },
    {
      id: '63adab96b65c28c168442316',
      name: 'coolest-project',
      blockName: 'coolest-project',
      blockType: 'textarea',
      label: "What's the coolest project you've built with Payload so far?",
      required: false,
      width: 100,
    },
    {
      id: '63adb90db65c28c168442322',
      blockName: 'farewell',
      blockType: 'message',
      message: {
        root: {
          type: 'root',
          children: [
            {
              type: 'paragraph',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Have a great rest of your day!',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              textFormat: 0,
              version: 1,
            },
            {
              type: 'paragraph',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Sincerely, \n\nPayload Team.',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              textFormat: 0,
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          version: 1,
        },
      },
    },
  ],
  redirect: {},
  submitButtonLabel: 'Submit',
  title: 'Basic Form',
  updatedAt: '2023-01-12T21:25:41.113Z',
}
```

## File: src/seed/contact.ts
```typescript
export const contact = {
  slug: 'contact',
  _status: 'published',
  layout: [
    {
      id: '63adc92568224b995af9df13',
      blockType: 'formBlock',
      enableIntro: true,
      form: '{{CONTACT_FORM_ID}}',
      introContent: {
        root: {
          type: 'root',
          children: [
            {
              type: 'heading',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Example contact form:',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              tag: 'h3',
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          version: 1,
        },
      },
    },
  ],
  title: 'Contact',
}
```

## File: src/seed/contactForm.ts
```typescript
export const contactForm = {
  id: '63c07ffd4cb6b574b4977573',
  confirmationMessage: {
    root: {
      type: 'root',
      children: [
        {
          type: 'heading',
          children: [
            {
              type: 'text',
              detail: 0,
              format: 0,
              mode: 'normal',
              style: '',
              text: 'The contact form has been submitted successfully.',
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          tag: 'h2',
          version: 1,
        },
      ],
      direction: 'ltr',
      format: '',
      indent: 0,
      version: 1,
    },
  },
  confirmationType: 'message',
  createdAt: '2023-01-12T21:47:41.374Z',
  emails: [
    {
      id: '6644edb9cffd2c6c48a44730',
      emailFrom: '"Payload" \u003Cdemo@payloadcms.com\u003E',
      emailTo: '{{email}}',
      message: {
        root: {
          type: 'root',
          children: [
            {
              type: 'paragraph',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Your contact form submission was successfully received.',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              textFormat: 0,
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          version: 1,
        },
      },
      subject: "You've received a new message.",
    },
  ],
  fields: [
    {
      id: '63c07f4e69853127a889530c',
      name: 'full-name',
      blockName: 'full-name',
      blockType: 'text',
      label: 'Full Name',
      required: true,
      width: 100,
    },
    {
      id: '63c07f7069853127a889530d',
      name: 'email',
      blockName: 'email',
      blockType: 'email',
      label: 'Email',
      required: true,
      width: 50,
    },
    {
      id: '63c07f8169853127a889530e',
      name: 'phone',
      blockName: 'phone',
      blockType: 'number',
      label: 'Phone',
      required: false,
      width: 50,
    },
    {
      id: '63c07f9d69853127a8895310',
      name: 'message',
      blockName: 'message',
      blockType: 'textarea',
      label: 'Message',
      required: true,
      width: 100,
    },
  ],
  redirect: {},
  submitButtonLabel: 'Submit',
  title: 'Contact Form',
  updatedAt: '2023-01-12T21:47:41.374Z',
}
```

## File: src/seed/home.ts
```typescript
export const home = {
  slug: 'home',
  _status: 'published',
  layout: [
    {
      id: '63adc92568224b995af9df12',
      blockType: 'formBlock',
      enableIntro: true,
      form: '{{BASIC_FORM_ID}}',
      introContent: {
        root: {
          type: 'root',
          children: [
            {
              type: 'heading',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Example basic form:',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              tag: 'h3',
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          version: 1,
        },
      },
    },
  ],
  title: 'Home',
}
```

## File: src/seed/signUp.ts
```typescript
export const signUp = {
  slug: 'sign-up',
  _status: 'published',
  layout: [
    {
      id: '63adc92568224b995af9df15',
      blockType: 'formBlock',
      enableIntro: true,
      form: '{{SIGNUP_FORM_ID}}',
      introContent: {
        root: {
          type: 'root',
          children: [
            {
              type: 'heading',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Example sign-up form:',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              tag: 'h3',
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          version: 1,
        },
      },
    },
  ],
  title: 'Sign Up',
}
```

## File: src/seed/signUpForm.ts
```typescript
export const signUpForm = {
  id: '63c086c36955e39c4208aa8f',
  confirmationMessage: {
    root: {
      type: 'root',
      children: [
        {
          type: 'heading',
          children: [
            {
              type: 'text',
              detail: 0,
              format: 0,
              mode: 'normal',
              style: '',
              text: 'Your sign up submission was successful.',
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          tag: 'h2',
          version: 1,
        },
      ],
      direction: 'ltr',
      format: '',
      indent: 0,
      version: 1,
    },
  },
  confirmationType: 'message',
  createdAt: '2023-01-12T22:16:35.480Z',
  emails: [
    {
      id: '6644edb9cffd2c6c48a44730',
      emailFrom: '"Payload" \u003Cdemo@payloadcms.com\u003E',
      emailTo: '{{email}}',
      message: {
        root: {
          type: 'root',
          children: [
            {
              type: 'paragraph',
              children: [
                {
                  type: 'text',
                  detail: 0,
                  format: 0,
                  mode: 'normal',
                  style: '',
                  text: 'Your sign up submission was received successfully.',
                  version: 1,
                },
              ],
              direction: 'ltr',
              format: '',
              indent: 0,
              textFormat: 0,
              version: 1,
            },
          ],
          direction: 'ltr',
          format: '',
          indent: 0,
          version: 1,
        },
      },
      subject: "You've received a new message.",
    },
    {
      id: '6644edb9cffd2c6c48a4472f',
      subject: "You've received a new message.",
    },
  ],
  fields: [
    {
      id: '63c085ae69853127a889531e',
      name: 'full-name',
      blockName: 'full-name',
      blockType: 'text',
      label: 'Full Name',
      required: true,
      width: 100,
    },
    {
      id: '63c085df69853127a889531f',
      name: 'email',
      blockName: 'email',
      blockType: 'email',
      label: 'Email',
      required: true,
      width: 100,
    },
    {
      id: '63c0861869853127a8895321',
      name: 'password',
      blockName: 'password',
      blockType: 'text',
      label: 'Password',
      required: true,
      width: 100,
    },
    {
      id: '63c0865769853127a8895324',
      blockType: 'message',
      message: [
        {
          children: [
            {
              text: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.',
            },
          ],
        },
      ],
    },
    {
      id: '63c086a469853127a8895325',
      name: 'terms-and-conditions',
      blockName: 'terms-and-conditions',
      blockType: 'checkbox',
      label: 'I agree to the terms and conditions',
      required: true,
    },
  ],
  redirect: {},
  submitButtonLabel: 'Create Account',
  title: 'Sign Up Form',
  updatedAt: '2023-01-12T22:16:35.480Z',
}
```

## File: src/utilities/deepMerge.ts
```typescript
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
```

## File: src/utilities/formatSlug.ts
```typescript
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
      const fallbackData = (data && data[fallback]) || (originalDoc && originalDoc[fallback])

      if (fallbackData && typeof fallbackData === 'string') {
        return format(fallbackData)
      }
    }

    return value
  }
```

## File: src/utilities/getGlobals.ts
```typescript
import type { Config } from 'src/payload-types'

import { unstable_cache } from 'next/cache'
import { getPayload } from 'payload'

import configPromise from '../payload.config'

type Global = keyof Config['globals']

async function getGlobal(slug: Global, depth = 0) {
  const payload = await getPayload({ config: configPromise })

  const global = await payload.findGlobal({
    slug,
    depth,
  })

  return global
}

/**
 * Returns a unstable_cache function mapped with the cache tag for the slug
 */
export const getCachedGlobal = (slug: Global, depth = 0) =>
  unstable_cache(async () => getGlobal(slug, depth), [slug], {
    tags: [`global_${slug}`],
  })
```

## File: src/utilities/toKebabCase.ts
```typescript
export const toKebabCase = (string) =>
  string
    ?.replace(/([a-z])([A-Z])/g, '$1-$2')
    .replace(/\s+/g, '-')
    .toLowerCase()
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

export interface Config {
  auth: {
    users: UserAuthOperations;
  };
  collections: {
    pages: Page;
    users: User;
    forms: Form;
    'form-submissions': FormSubmission;
    'payload-locked-documents': PayloadLockedDocument;
    'payload-preferences': PayloadPreference;
    'payload-migrations': PayloadMigration;
  };
  collectionsJoins: {};
  collectionsSelect: {
    pages: PagesSelect<false> | PagesSelect<true>;
    users: UsersSelect<false> | UsersSelect<true>;
    forms: FormsSelect<false> | FormsSelect<true>;
    'form-submissions': FormSubmissionsSelect<false> | FormSubmissionsSelect<true>;
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
  layout: {
    form: string | Form;
    enableIntro?: boolean | null;
    introContent?: {
      root: {
        type: string;
        children: {
          type: string;
          version: number;
          [k: string]: unknown;
        }[];
        direction: ('ltr' | 'rtl') | null;
        format: 'left' | 'start' | 'center' | 'right' | 'end' | 'justify' | '';
        indent: number;
        version: number;
      };
      [k: string]: unknown;
    } | null;
    id?: string | null;
    blockName?: string | null;
    blockType: 'formBlock';
  }[];
  slug?: string | null;
  updatedAt: string;
  createdAt: string;
  _status?: ('draft' | 'published') | null;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "forms".
 */
export interface Form {
  id: string;
  title: string;
  fields?:
    | (
        | {
            name: string;
            label?: string | null;
            width?: number | null;
            required?: boolean | null;
            defaultValue?: boolean | null;
            id?: string | null;
            blockName?: string | null;
            blockType: 'checkbox';
          }
        | {
            name: string;
            label?: string | null;
            width?: number | null;
            required?: boolean | null;
            id?: string | null;
            blockName?: string | null;
            blockType: 'country';
          }
        | {
            name: string;
            label?: string | null;
            width?: number | null;
            required?: boolean | null;
            id?: string | null;
            blockName?: string | null;
            blockType: 'email';
          }
        | {
            message?: {
              root: {
                type: string;
                children: {
                  type: string;
                  version: number;
                  [k: string]: unknown;
                }[];
                direction: ('ltr' | 'rtl') | null;
                format: 'left' | 'start' | 'center' | 'right' | 'end' | 'justify' | '';
                indent: number;
                version: number;
              };
              [k: string]: unknown;
            } | null;
            id?: string | null;
            blockName?: string | null;
            blockType: 'message';
          }
        | {
            name: string;
            label?: string | null;
            width?: number | null;
            defaultValue?: number | null;
            required?: boolean | null;
            id?: string | null;
            blockName?: string | null;
            blockType: 'number';
          }
        | {
            name: string;
            label?: string | null;
            width?: number | null;
            defaultValue?: string | null;
            options?:
              | {
                  label: string;
                  value: string;
                  id?: string | null;
                }[]
              | null;
            required?: boolean | null;
            id?: string | null;
            blockName?: string | null;
            blockType: 'select';
          }
        | {
            name: string;
            label?: string | null;
            width?: number | null;
            required?: boolean | null;
            id?: string | null;
            blockName?: string | null;
            blockType: 'state';
          }
        | {
            name: string;
            label?: string | null;
            width?: number | null;
            defaultValue?: string | null;
            required?: boolean | null;
            id?: string | null;
            blockName?: string | null;
            blockType: 'text';
          }
        | {
            name: string;
            label?: string | null;
            width?: number | null;
            defaultValue?: string | null;
            required?: boolean | null;
            id?: string | null;
            blockName?: string | null;
            blockType: 'textarea';
          }
      )[]
    | null;
  submitButtonLabel?: string | null;
  confirmationType?: ('message' | 'redirect') | null;
  confirmationMessage?: {
    root: {
      type: string;
      children: {
        type: string;
        version: number;
        [k: string]: unknown;
      }[];
      direction: ('ltr' | 'rtl') | null;
      format: 'left' | 'start' | 'center' | 'right' | 'end' | 'justify' | '';
      indent: number;
      version: number;
    };
    [k: string]: unknown;
  } | null;
  redirect?: {
    url: string;
  };
  emails?:
    | {
        emailTo?: string | null;
        cc?: string | null;
        bcc?: string | null;
        replyTo?: string | null;
        emailFrom?: string | null;
        subject: string;
        message?: {
          root: {
            type: string;
            children: {
              type: string;
              version: number;
              [k: string]: unknown;
            }[];
            direction: ('ltr' | 'rtl') | null;
            format: 'left' | 'start' | 'center' | 'right' | 'end' | 'justify' | '';
            indent: number;
            version: number;
          };
          [k: string]: unknown;
        } | null;
        id?: string | null;
      }[]
    | null;
  updatedAt: string;
  createdAt: string;
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
 * via the `definition` "form-submissions".
 */
export interface FormSubmission {
  id: string;
  form: string | Form;
  submissionData?:
    | {
        field: string;
        value: string;
        id?: string | null;
      }[]
    | null;
  updatedAt: string;
  createdAt: string;
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
      } | null)
    | ({
        relationTo: 'forms';
        value: string | Form;
      } | null)
    | ({
        relationTo: 'form-submissions';
        value: string | FormSubmission;
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
  layout?:
    | T
    | {
        formBlock?:
          | T
          | {
              form?: T;
              enableIntro?: T;
              introContent?: T;
              id?: T;
              blockName?: T;
            };
      };
  slug?: T;
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
 * via the `definition` "forms_select".
 */
export interface FormsSelect<T extends boolean = true> {
  title?: T;
  fields?:
    | T
    | {
        checkbox?:
          | T
          | {
              name?: T;
              label?: T;
              width?: T;
              required?: T;
              defaultValue?: T;
              id?: T;
              blockName?: T;
            };
        country?:
          | T
          | {
              name?: T;
              label?: T;
              width?: T;
              required?: T;
              id?: T;
              blockName?: T;
            };
        email?:
          | T
          | {
              name?: T;
              label?: T;
              width?: T;
              required?: T;
              id?: T;
              blockName?: T;
            };
        message?:
          | T
          | {
              message?: T;
              id?: T;
              blockName?: T;
            };
        number?:
          | T
          | {
              name?: T;
              label?: T;
              width?: T;
              defaultValue?: T;
              required?: T;
              id?: T;
              blockName?: T;
            };
        select?:
          | T
          | {
              name?: T;
              label?: T;
              width?: T;
              defaultValue?: T;
              options?:
                | T
                | {
                    label?: T;
                    value?: T;
                    id?: T;
                  };
              required?: T;
              id?: T;
              blockName?: T;
            };
        state?:
          | T
          | {
              name?: T;
              label?: T;
              width?: T;
              required?: T;
              id?: T;
              blockName?: T;
            };
        text?:
          | T
          | {
              name?: T;
              label?: T;
              width?: T;
              defaultValue?: T;
              required?: T;
              id?: T;
              blockName?: T;
            };
        textarea?:
          | T
          | {
              name?: T;
              label?: T;
              width?: T;
              defaultValue?: T;
              required?: T;
              id?: T;
              blockName?: T;
            };
      };
  submitButtonLabel?: T;
  confirmationType?: T;
  confirmationMessage?: T;
  redirect?:
    | T
    | {
        url?: T;
      };
  emails?:
    | T
    | {
        emailTo?: T;
        cc?: T;
        bcc?: T;
        replyTo?: T;
        emailFrom?: T;
        subject?: T;
        message?: T;
        id?: T;
      };
  updatedAt?: T;
  createdAt?: T;
}
/**
 * This interface was referenced by `Config`'s JSON-Schema
 * via the `definition` "form-submissions_select".
 */
export interface FormSubmissionsSelect<T extends boolean = true> {
  form?: T;
  submissionData?:
    | T
    | {
        field?: T;
        value?: T;
        id?: T;
      };
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
```

## File: src/payload.config.ts
```typescript
import { mongooseAdapter } from '@payloadcms/db-mongodb'
import { formBuilderPlugin } from '@payloadcms/plugin-form-builder'
import {
  BoldFeature,
  FixedToolbarFeature,
  HeadingFeature,
  InlineToolbarFeature,
  ItalicFeature,
  lexicalEditor,
  LinkFeature,
} from '@payloadcms/richtext-lexical'
import path from 'path'
import { buildConfig } from 'payload'
import { fileURLToPath } from 'url'

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
    user: Users.slug,
  },
  collections: [Pages, Users],
  // We need to set CORS rules pointing to our hosted domains for the frontend to be able to submit to our API
  cors: [process.env.NEXT_PUBLIC_PAYLOAD_URL || ''],
  db: mongooseAdapter({
    url: process.env.DATABASE_URI || '',
  }),
  editor: lexicalEditor({
    features: () => {
      return [
        BoldFeature(),
        ItalicFeature(),
        LinkFeature({ enabledCollections: ['pages'] }),
        HeadingFeature({ enabledHeadingSizes: ['h1', 'h2', 'h3'] }),
        FixedToolbarFeature(),
        InlineToolbarFeature(),
      ]
    },
  }),
  globals: [MainMenu],
  graphQL: {
    schemaOutputFile: path.resolve(dirname, 'generated-schema.graphql'),
  },
  plugins: [
    formBuilderPlugin({
      fields: {
        payment: false,
      },
      formOverrides: {
        fields: undefined,
      },
    }),
  ],
  secret: process.env.PAYLOAD_SECRET || '',
  typescript: {
    outputFile: path.resolve(dirname, 'payload-types.ts'),
  },
})
```

## File: .env.example
```
DATABASE_URI=mongodb://127.0.0.1/payload-form-builder-example
PAYLOAD_SECRET=YOUR_SECRET_HERE
NEXT_PUBLIC_PAYLOAD_URL=http://localhost:3000
```

## File: .eslintrc.cjs
```
module.exports = {
  extends: 'next',
  root: true,
  parserOptions: {
    project: ['./tsconfig.json'],
    tsconfigRootDir: __dirname,
  },
}
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
  "name": "payload-form-builder-example",
  "version": "1.0.0",
  "description": "Payload form builder example.",
  "license": "MIT",
  "type": "module",
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
    "@apollo/client": "^3.7.0",
    "@faceless-ui/css-grid": "^1.2.0",
    "@faceless-ui/modal": "^2.0.2",
    "@payloadcms/db-mongodb": "latest",
    "@payloadcms/next": "latest",
    "@payloadcms/plugin-cloud": "latest",
    "@payloadcms/plugin-form-builder": "latest",
    "@payloadcms/richtext-lexical": "latest",
    "cross-env": "^7.0.3",
    "graphql": "^16.9.0",
    "next": "^15.0.0",
    "payload": "latest",
    "react": "19.0.0",
    "react-dom": "19.0.0",
    "react-hook-form": "^7.41.0",
    "react-select": "^5.9.0"
  },
  "devDependencies": {
    "@payloadcms/graphql": "latest",
    "@types/node": "^20.11.25",
    "@types/react": "19.0.1",
    "@types/react-dom": "19.0.1",
    "dotenv": "^16.4.5",
    "eslint": "^8.57.0",
    "eslint-config-next": "^15.0.0",
    "tsx": "^4.16.2",
    "typescript": "5.5.2"
  },
  "engines": {
    "node": "^18.20.2 || >=20.9.0"
  }
}
```

## File: README.md
```markdown
# Payload Form Builder Example

The [Payload Form Builder Example](https://github.com/payloadcms/payload/tree/main/examples/form-builder/payload) demonstrates how to implement the official [Form Builder Plugin](https://payloadcms.com/docs/plugins/form-builder) in [Payload](https://github.com/payloadcms/payload).

**IMPORTANT—This example includes a fully integrated Next.js App Router front-end that runs on the same server as Payload.**

## Quick Start

1. Run the following command to create a project from the example:

- `npx create-payload-app --example form-builder`

2. `cp .env.example .env` to copy the example environment variables

3. `pnpm dev`, `yarn dev` or `npm run dev` to start the server
   - Press `y` when prompted to seed the database
4. `open http://localhost:3000` to access the home page
5. `open http://localhost:3000/admin` to access the admin panel
   - Login with email `demo@payloadcms.com` and password `demo`

That's it! Changes made in `./src` will be

## How it works

The [Form Builder Plugin](https://payloadcms.com/docs/plugins/form-builder) automatically adds the `forms` and `formSubmissions` collections to your config which your front-end can use to query forms and submit form data. You can embed forms into layout building blocks by referring a `forms` document in a relationship field.

See the official [Form Builder Plugin](https://payloadcms.com/docs/plugins/form-builder) for full details.

## Development

To spin up this example locally, follow the [Quick Start](#quick-start).

### Seed

On boot, a seed script is included to scaffold a basic database for you to use as an example. You can remove `pnpm seed` from the `dev` script in the `package.json` to prevent this behavior. You can also freshly seed your project at any time by running `pnpm seed`. This seed creates a user with email `demo@payloadcms.com` and password `demo` along with a home page and an example page with two versions, one published and the other draft.

> NOTICE: seeding the database is destructive because it drops your current database to populate a fresh one from the seed template. Only run this command if you are starting a new project or can afford to lose your current data.

## Production

To run Payload in production, you need to build and serve the Admin panel. To do so, follow these steps:

1. Invoke the `next build` script by running `pnpm build` or `npm run build` in your project root. This creates a `.next` directory with a production-ready admin bundle.
1. Finally run `pnpm start` or `npm run start` to run Node in production and serve Payload from the `.build` directory.

### Deployment

The easiest way to deploy your project is to use [Payload Cloud](https://payloadcms.com/new/import), a one-click hosting solution to deploy production-ready instances of your Payload apps directly from your GitHub repo. You can also choose to self-host your app, check out the [Deployment](https://payloadcms.com/docs/production/deployment) docs for more details.

## Questions

If you have any issues or questions, reach out to us on [Discord](https://discord.com/invite/payload) or start a [GitHub discussion](https://github.com/payloadcms/payload/discussions).
```

## File: tsconfig.json
```json
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
```
