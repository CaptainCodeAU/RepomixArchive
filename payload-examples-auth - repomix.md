This file is a merged representation of the `payload/examples/auth/` codebase (https://github.com/payloadcms/payload/tree/main/examples/auth), combined into a single document by Repomix. This Payload Auth Example demonstrates how to implement Payload Authentication into all types of applications.

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
      _components/
        Button/
          index.module.scss
          index.tsx
        Gutter/
          index.module.scss
          index.tsx
        Header/
          Nav/
            index.module.scss
            index.tsx
          index.module.scss
          index.tsx
        HydrateClientUser/
          index.tsx
        Input/
          index.module.scss
          index.tsx
        Message/
          index.module.scss
          index.tsx
        RenderParams/
          index.tsx
        RichText/
          index.module.scss
          index.tsx
          serialize.tsx
      _css/
        app.scss
        colors.scss
        common.scss
        queries.scss
        theme.scss
        type.scss
      _providers/
        Auth/
          gql.ts
          index.tsx
          rest.ts
          types.ts
      account/
        AccountForm/
          index.module.scss
          index.tsx
        index.module.scss
        page.tsx
      create-account/
        CreateAccountForm/
          index.module.scss
          index.tsx
        index.module.scss
        page.tsx
      login/
        LoginForm/
          index.module.scss
          index.tsx
        index.module.scss
        page.tsx
      logout/
        LogoutPage/
          index.tsx
        index.module.scss
        page.tsx
      recover-password/
        RecoverPasswordForm/
          index.module.scss
          index.tsx
        index.module.scss
        page.tsx
      reset-password/
        ResetPasswordForm/
          index.module.scss
          index.tsx
        index.module.scss
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
  collections/
    access/
      admins.ts
      adminsAndUser.ts
      anyone.ts
      checkRole.ts
    hooks/
      loginAfterCreate.ts
      protectRoles.ts
    Users.ts
  components/
    BeforeLogin/
      index.tsx
  migrations/
    seed.ts
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

## File: src/app/(app)/_components/Button/index.module.scss
````scss
@import '../../_css/type.scss';

.button {
  border: none;
  cursor: pointer;
  display: inline-flex;
  justify-content: center;
  background-color: transparent;
  text-decoration: none;
  display: inline-flex;
  padding: 12px 24px;
}

.content {
  display: flex;
  align-items: center;
  justify-content: space-around;
}

.label {
  @extend %label;
  text-align: center;
  display: flex;
  align-items: center;
}

.appearance--primary {
  background-color: var(--theme-elevation-1000);
  color: var(--theme-elevation-0);
}

.appearance--secondary {
  background-color: transparent;
  box-shadow: inset 0 0 0 1px var(--theme-elevation-1000);
}

.appearance--default {
  padding: 0;
  color: var(--theme-text);
}
````

## File: src/app/(app)/_components/Button/index.tsx
````typescript
'use client'

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
  invert?: boolean
  label?: string
  newTab?: boolean
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
  invert,
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
    invert && classes[`${appearance}--invert`],
  ]
    .filter(Boolean)
    .join(' ')

  const content = (
    <div className={classes.content}>
      <span className={classes.label}>{label}</span>
    </div>
  )

  if (onClick || type === 'submit') {el = 'button'}

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

## File: src/app/(app)/_components/Gutter/index.module.scss
````scss
.gutter {
  max-width: 1920px;
  margin-left: auto;
  margin-right: auto;
}

.gutterLeft {
  padding-left: var(--gutter-h);
}

.gutterRight {
  padding-right: var(--gutter-h);
}
````

## File: src/app/(app)/_components/Gutter/index.tsx
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

## File: src/app/(app)/_components/Header/Nav/index.module.scss
````scss
@use '../../../_css/queries.scss' as *;

.nav {
  display: flex;
  gap: calc(var(--base) / 4) var(--base);
  align-items: center;
  flex-wrap: wrap;
  opacity: 1;
  transition: opacity 100ms linear;
  visibility: visible;

  > * {
    text-decoration: none;
  }
}

.hide {
  opacity: 0;
  visibility: hidden;
}
````

## File: src/app/(app)/_components/Header/Nav/index.tsx
````typescript
'use client'

import Link from 'next/link'
import React from 'react'

import { useAuth } from '../../../_providers/Auth'
import classes from './index.module.scss'

export const HeaderNav: React.FC = () => {
  const { user } = useAuth()

  return (
    <nav
      className={[
        classes.nav,
        // fade the nav in on user load to avoid flash of content and layout shift
        // Vercel also does this in their own website header, see https://vercel.com
        user === undefined && classes.hide,
      ]
        .filter(Boolean)
        .join(' ')}
    >
      {user && (
        <React.Fragment>
          <Link href="/account">Account</Link>
          <Link href="/logout">Logout</Link>
        </React.Fragment>
      )}
      {!user && (
        <React.Fragment>
          <Link href="/login">Login</Link>
          <Link href="/create-account">Create Account</Link>
        </React.Fragment>
      )}
    </nav>
  )
}
````

## File: src/app/(app)/_components/Header/index.module.scss
````scss
@use '../../_css/queries.scss' as *;

.header {
  padding: var(--base) 0;
}

.wrap {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: calc(var(--base) / 2) var(--base);
}

.logo {
  width: 150px;
}

:global([data-theme='light']) {
  .logo {
    filter: invert(1);
  }
}
````

## File: src/app/(app)/_components/Header/index.tsx
````typescript
import Image from 'next/image'
import Link from 'next/link'
import React from 'react'

import { Gutter } from '../Gutter'
import { HeaderNav } from './Nav'
import classes from './index.module.scss'

export const Header = () => {
  return (
    <header className={classes.header}>
      <Gutter className={classes.wrap}>
        <Link className={classes.logo} href="/">
          <picture>
            <source
              media="(prefers-color-scheme: dark)"
              srcSet="https://raw.githubusercontent.com/payloadcms/payload/master/src/admin/assets/images/payload-logo-light.svg"
            />
            <Image
              alt="Payload Logo"
              height={30}
              src="https://raw.githubusercontent.com/payloadcms/payload/master/src/admin/assets/images/payload-logo-dark.svg"
              width={150}
            />
          </picture>
        </Link>
        <HeaderNav />
      </Gutter>
    </header>
  )
}
````

## File: src/app/(app)/_components/HydrateClientUser/index.tsx
````typescript
'use client'

import type { Permissions } from 'payload/auth'
import type { PayloadRequest } from 'payload/types'

import { useEffect } from 'react'

import { useAuth } from '../../_providers/Auth'

export const HydrateClientUser: React.FC<{
  permissions: Permissions
  user: PayloadRequest['user']
}> = ({ permissions, user }) => {
  const { setPermissions, setUser } = useAuth()

  useEffect(() => {
    setUser(user)
    setPermissions(permissions)
  }, [user, permissions, setUser, setPermissions])

  return null
}
````

## File: src/app/(app)/_components/Input/index.module.scss
````scss
@import '../../_css/common';

.inputWrap {
  width: 100%;
}

.input {
  width: 100%;
  font-family: system-ui;
  border-radius: 0;
  box-shadow: none;
  border: none;
  background: none;
  background-color: var(--theme-elevation-100);
  color: var(--theme-elevation-1000);
  height: calc(var(--base) * 2);
  line-height: calc(var(--base) * 2);
  padding: 0 calc(var(--base) / 2);

  &:focus {
    border: none;
    outline: none;
  }

  &:-webkit-autofill,
  &:-webkit-autofill:hover,
  &:-webkit-autofill:focus {
    -webkit-text-fill-color: var(--theme-text);
    -webkit-box-shadow: 0 0 0px 1000px var(--theme-elevation-150) inset;
    transition: background-color 5000s ease-in-out 0s;
  }
}

@media (prefers-color-scheme: dark) {
  .input {
    background-color: var(--theme-elevation-150);
  }
}

.error {
  background-color: var(--theme-error-150);
}

.label {
  margin-bottom: 0;
  display: block;
  line-height: 1;
  margin-bottom: calc(var(--base) / 2);
}

.errorMessage {
  font-size: small;
  line-height: 1.25;
  margin-top: 4px;
  color: red;
}
````

## File: src/app/(app)/_components/Input/index.tsx
````typescript
import type { FieldValues, UseFormRegister } from 'react-hook-form'

import React from 'react'

import classes from './index.module.scss'

type Props = {
  error: any
  label: string
  name: string
  register: UseFormRegister<any & FieldValues> // eslint-disable-line @typescript-eslint/no-redundant-type-constituents
  required?: boolean
  type?: 'email' | 'number' | 'password' | 'text'
  validate?: (value: string) => boolean | string
}

export const Input: React.FC<Props> = ({
  name,
  type = 'text',
  error,
  label,
  register,
  required,
  validate,
}) => {
  return (
    <div className={classes.inputWrap}>
      <label className={classes.label} htmlFor="name">
        {`${label} ${required ? '*' : ''}`}
      </label>
      <input
        className={[classes.input, error && classes.error].filter(Boolean).join(' ')}
        {...{ type }}
        {...register(name, {
          required,
          validate,
          ...(type === 'email'
            ? {
                pattern: {
                  message: 'Please enter a valid email',
                  value: /\S[^\s@]*@\S+\.\S+/,
                },
              }
            : {}),
        })}
      />
      {error && (
        <div className={classes.errorMessage}>
          {!error?.message && error?.type === 'required'
            ? 'This field is required'
            : error?.message}
        </div>
      )}
    </div>
  )
}
````

## File: src/app/(app)/_components/Message/index.module.scss
````scss
@import '../../_css/common';

.message {
  padding: calc(var(--base) / 2) calc(var(--base) / 2);
  line-height: 1.25;
  width: 100%;
}

.default {
  background-color: var(--theme-elevation-100);
  color: var(--theme-elevation-1000);
}

.warning {
  background-color: var(--theme-warning-500);
  color: var(--theme-warning-900);
}

.error {
  background-color: var(--theme-error-500);
  color: var(--theme-error-900);
}

.success {
  background-color: var(--theme-success-500);
  color: var(--theme-success-900);
}

@media (prefers-color-scheme: dark) {
  .default {
    background-color: var(--theme-elevation-900);
    color: var(--theme-elevation-100);
  }

  .warning {
    color: var(--theme-warning-100);
  }

  .error {
    color: var(--theme-error-100);
  }

  .success {
    color: var(--theme-success-100);
  }
}
````

## File: src/app/(app)/_components/Message/index.tsx
````typescript
import React from 'react'

import classes from './index.module.scss'

export const Message: React.FC<{
  className?: string
  error?: React.ReactNode
  message?: React.ReactNode
  success?: React.ReactNode
  warning?: React.ReactNode
}> = ({ className, error, message, success, warning }) => {
  const messageToRender = message || error || success || warning

  if (messageToRender) {
    return (
      <div
        className={[
          classes.message,
          className,
          error && classes.error,
          success && classes.success,
          warning && classes.warning,
          !error && !success && !warning && classes.default,
        ]
          .filter(Boolean)
          .join(' ')}
      >
        {messageToRender}
      </div>
    )
  }
  return null
}
````

## File: src/app/(app)/_components/RenderParams/index.tsx
````typescript
'use client'
import { useSearchParams } from 'next/navigation'
import React from 'react'

import { Message } from '../Message'

export const RenderParams: React.FC<{
  className?: string
  message?: string
  params?: string[]
}> = ({ className, message, params = ['error', 'message', 'success'] }) => {
  const searchParams = useSearchParams()
  const paramValues = params.map((param) => searchParams.get(param)).filter(Boolean)

  if (paramValues.length) {
    return (
      <div className={className}>
        {paramValues.map((paramValue) => (
          <Message
            key={paramValue}
            message={(message || 'PARAM')?.replace('PARAM', paramValue || '')}
          />
        ))}
      </div>
    )
  }

  return null
}
````

## File: src/app/(app)/_components/RichText/index.module.scss
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

## File: src/app/(app)/_components/RichText/index.tsx
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

## File: src/app/(app)/_components/RichText/serialize.tsx
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
      case 'blockquote':
        return <blockquote key={i}>{serialize(node.children)}</blockquote>
      case 'ul':
        return <ul key={i}>{serialize(node.children)}</ul>
      case 'ol':
        return <ol key={i}>{serialize(node.children)}</ol>
      case 'li':
        return <li key={i}>{serialize(node.children)}</li>
      case 'link':
        return (
          <a href={escapeHTML(node.url)} key={i}>
            {serialize(node.children)}
          </a>
        )

      default:
        return <p key={i}>{serialize(node.children)}</p>
    }
  })

export default serialize
````

## File: src/app/(app)/_css/app.scss
````scss
@use './queries.scss' as *;
@use './colors.scss' as *;
@use './type.scss' as *;
@import './theme.scss';

:root {
  --base: 24px;
  --font-body: system-ui;
  --font-mono: 'Roboto Mono', monospace;

  --gutter-h: 180px;
  --block-padding: 120px;

  @include large-break {
    --gutter-h: 144px;
    --block-padding: 96px;
  }

  @include mid-break {
    --gutter-h: 24px;
    --block-padding: 60px;
  }
}

* {
  box-sizing: border-box;
}

html {
  @extend %body;
  background: var(--theme-bg);
  -webkit-font-smoothing: antialiased;
}

html,
body,
#app {
  height: 100%;
}

body {
  font-family: var(--font-body);
  margin: 0;
  color: var(--theme-text);
}

::selection {
  background: var(--theme-success-500);
  color: var(--color-base-800);
}

::-moz-selection {
  background: var(--theme-success-500);
  color: var(--color-base-800);
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
````

## File: src/app/(app)/_css/colors.scss
````scss
:root {
  --color-base-0: rgb(255, 255, 255);
  --color-base-50: rgb(245, 245, 245);
  --color-base-100: rgb(235, 235, 235);
  --color-base-150: rgb(221, 221, 221);
  --color-base-200: rgb(208, 208, 208);
  --color-base-250: rgb(195, 195, 195);
  --color-base-300: rgb(181, 181, 181);
  --color-base-350: rgb(168, 168, 168);
  --color-base-400: rgb(154, 154, 154);
  --color-base-450: rgb(141, 141, 141);
  --color-base-500: rgb(128, 128, 128);
  --color-base-550: rgb(114, 114, 114);
  --color-base-600: rgb(101, 101, 101);
  --color-base-650: rgb(87, 87, 87);
  --color-base-700: rgb(74, 74, 74);
  --color-base-750: rgb(60, 60, 60);
  --color-base-800: rgb(47, 47, 47);
  --color-base-850: rgb(34, 34, 34);
  --color-base-900: rgb(20, 20, 20);
  --color-base-950: rgb(7, 7, 7);
  --color-base-1000: rgb(0, 0, 0);

  --color-success-50: rgb(237, 245, 249);
  --color-success-100: rgb(218, 237, 248);
  --color-success-150: rgb(188, 225, 248);
  --color-success-200: rgb(156, 216, 253);
  --color-success-250: rgb(125, 204, 248);
  --color-success-300: rgb(97, 190, 241);
  --color-success-350: rgb(65, 178, 236);
  --color-success-400: rgb(36, 164, 223);
  --color-success-450: rgb(18, 148, 204);
  --color-success-500: rgb(21, 135, 186);
  --color-success-550: rgb(12, 121, 168);
  --color-success-600: rgb(11, 110, 153);
  --color-success-650: rgb(11, 97, 135);
  --color-success-700: rgb(17, 88, 121);
  --color-success-750: rgb(17, 76, 105);
  --color-success-800: rgb(18, 66, 90);
  --color-success-850: rgb(18, 56, 76);
  --color-success-900: rgb(19, 44, 58);
  --color-success-950: rgb(22, 33, 39);

  --color-error-50: rgb(250, 241, 240);
  --color-error-100: rgb(252, 229, 227);
  --color-error-150: rgb(247, 208, 204);
  --color-error-200: rgb(254, 193, 188);
  --color-error-250: rgb(253, 177, 170);
  --color-error-300: rgb(253, 154, 146);
  --color-error-350: rgb(253, 131, 123);
  --color-error-400: rgb(246, 109, 103);
  --color-error-450: rgb(234, 90, 86);
  --color-error-500: rgb(218, 75, 72);
  --color-error-550: rgb(200, 62, 61);
  --color-error-600: rgb(182, 54, 54);
  --color-error-650: rgb(161, 47, 47);
  --color-error-700: rgb(144, 44, 43);
  --color-error-750: rgb(123, 41, 39);
  --color-error-800: rgb(105, 39, 37);
  --color-error-850: rgb(86, 36, 33);
  --color-error-900: rgb(64, 32, 29);
  --color-error-950: rgb(44, 26, 24);

  --color-warning-50: rgb(249, 242, 237);
  --color-warning-100: rgb(248, 232, 219);
  --color-warning-150: rgb(243, 212, 186);
  --color-warning-200: rgb(243, 200, 162);
  --color-warning-250: rgb(240, 185, 136);
  --color-warning-300: rgb(238, 166, 98);
  --color-warning-350: rgb(234, 148, 58);
  --color-warning-400: rgb(223, 132, 17);
  --color-warning-450: rgb(204, 120, 15);
  --color-warning-500: rgb(185, 108, 13);
  --color-warning-550: rgb(167, 97, 10);
  --color-warning-600: rgb(150, 87, 11);
  --color-warning-650: rgb(134, 78, 11);
  --color-warning-700: rgb(120, 70, 13);
  --color-warning-750: rgb(105, 61, 13);
  --color-warning-800: rgb(90, 55, 19);
  --color-warning-850: rgb(73, 47, 21);
  --color-warning-900: rgb(56, 38, 20);
  --color-warning-950: rgb(38, 29, 21);

  --color-blue-50: rgb(237, 245, 249);
  --color-blue-100: rgb(218, 237, 248);
  --color-blue-150: rgb(188, 225, 248);
  --color-blue-200: rgb(156, 216, 253);
  --color-blue-250: rgb(125, 204, 248);
  --color-blue-300: rgb(97, 190, 241);
  --color-blue-350: rgb(65, 178, 236);
  --color-blue-400: rgb(36, 164, 223);
  --color-blue-450: rgb(18, 148, 204);
  --color-blue-500: rgb(21, 135, 186);
  --color-blue-550: rgb(12, 121, 168);
  --color-blue-600: rgb(11, 110, 153);
  --color-blue-650: rgb(11, 97, 135);
  --color-blue-700: rgb(17, 88, 121);
  --color-blue-750: rgb(17, 76, 105);
  --color-blue-800: rgb(18, 66, 90);
  --color-blue-850: rgb(18, 56, 76);
  --color-blue-900: rgb(19, 44, 58);
  --color-blue-950: rgb(22, 33, 39);
}
````

## File: src/app/(app)/_css/common.scss
````scss
@forward './queries.scss';
````

## File: src/app/(app)/_css/queries.scss
````scss
$breakpoint-xs-width: 400px;
$breakpoint-s-width: 768px;
$breakpoint-m-width: 1024px;
$breakpoint-l-width: 1440px;

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
````

## File: src/app/(app)/_css/theme.scss
````scss
@media (prefers-color-scheme: light) {
  :root {
    --theme-success-50: var(--color-success-50);
    --theme-success-100: var(--color-success-100);
    --theme-success-150: var(--color-success-150);
    --theme-success-200: var(--color-success-200);
    --theme-success-250: var(--color-success-250);
    --theme-success-300: var(--color-success-300);
    --theme-success-350: var(--color-success-350);
    --theme-success-400: var(--color-success-400);
    --theme-success-450: var(--color-success-450);
    --theme-success-500: var(--color-success-500);
    --theme-success-550: var(--color-success-550);
    --theme-success-600: var(--color-success-600);
    --theme-success-650: var(--color-success-650);
    --theme-success-700: var(--color-success-700);
    --theme-success-750: var(--color-success-750);
    --theme-success-800: var(--color-success-800);
    --theme-success-850: var(--color-success-850);
    --theme-success-900: var(--color-success-900);
    --theme-success-950: var(--color-success-950);

    --theme-warning-50: var(--color-warning-50);
    --theme-warning-100: var(--color-warning-100);
    --theme-warning-150: var(--color-warning-150);
    --theme-warning-200: var(--color-warning-200);
    --theme-warning-250: var(--color-warning-250);
    --theme-warning-300: var(--color-warning-300);
    --theme-warning-350: var(--color-warning-350);
    --theme-warning-400: var(--color-warning-400);
    --theme-warning-450: var(--color-warning-450);
    --theme-warning-500: var(--color-warning-500);
    --theme-warning-550: var(--color-warning-550);
    --theme-warning-600: var(--color-warning-600);
    --theme-warning-650: var(--color-warning-650);
    --theme-warning-700: var(--color-warning-700);
    --theme-warning-750: var(--color-warning-750);
    --theme-warning-800: var(--color-warning-800);
    --theme-warning-850: var(--color-warning-850);
    --theme-warning-900: var(--color-warning-900);
    --theme-warning-950: var(--color-warning-950);

    --theme-error-50: var(--color-error-50);
    --theme-error-100: var(--color-error-100);
    --theme-error-150: var(--color-error-150);
    --theme-error-200: var(--color-error-200);
    --theme-error-250: var(--color-error-250);
    --theme-error-300: var(--color-error-300);
    --theme-error-350: var(--color-error-350);
    --theme-error-400: var(--color-error-400);
    --theme-error-450: var(--color-error-450);
    --theme-error-500: var(--color-error-500);
    --theme-error-550: var(--color-error-550);
    --theme-error-600: var(--color-error-600);
    --theme-error-650: var(--color-error-650);
    --theme-error-700: var(--color-error-700);
    --theme-error-750: var(--color-error-750);
    --theme-error-800: var(--color-error-800);
    --theme-error-850: var(--color-error-850);
    --theme-error-900: var(--color-error-900);
    --theme-error-950: var(--color-error-950);

    --theme-elevation-0: var(--color-base-0);
    --theme-elevation-50: var(--color-base-50);
    --theme-elevation-100: var(--color-base-100);
    --theme-elevation-150: var(--color-base-150);
    --theme-elevation-200: var(--color-base-200);
    --theme-elevation-250: var(--color-base-250);
    --theme-elevation-300: var(--color-base-300);
    --theme-elevation-350: var(--color-base-350);
    --theme-elevation-400: var(--color-base-400);
    --theme-elevation-450: var(--color-base-450);
    --theme-elevation-500: var(--color-base-500);
    --theme-elevation-550: var(--color-base-550);
    --theme-elevation-600: var(--color-base-600);
    --theme-elevation-650: var(--color-base-650);
    --theme-elevation-700: var(--color-base-700);
    --theme-elevation-750: var(--color-base-750);
    --theme-elevation-800: var(--color-base-800);
    --theme-elevation-850: var(--color-base-850);
    --theme-elevation-900: var(--color-base-900);
    --theme-elevation-950: var(--color-base-950);
    --theme-elevation-1000: var(--color-base-1000);

    --theme-bg: var(--theme-elevation-0);
    --theme-input-bg: var(--theme-elevation-50);
    --theme-text: var(--theme-elevation-750);
    --theme-border-color: var(--theme-elevation-150);

    color-scheme: light;
    color: var(--theme-text);

    --highlight-default-bg-color: var(--theme-success-400);
    --highlight-default-text-color: var(--theme-text);

    --highlight-danger-bg-color: var(--theme-error-150);
    --highlight-danger-text-color: var(--theme-text);
  }

  h1 a,
  h2 a,
  h3 a,
  h4 a,
  h5 a,
  h6 a {
    color: var(--theme-elevation-750);

    &:hover {
      color: var(--theme-elevation-800);
    }

    &:visited {
      color: var(--theme-elevation-750);

      &:hover {
        color: var(--theme-elevation-800);
      }
    }
  }
}

@media (prefers-color-scheme: dark) {
  :root {
    --theme-elevation-0: var(--color-base-1000);
    --theme-elevation-50: var(--color-base-950);
    --theme-elevation-100: var(--color-base-900);
    --theme-elevation-150: var(--color-base-850);
    --theme-elevation-200: var(--color-base-800);
    --theme-elevation-250: var(--color-base-750);
    --theme-elevation-300: var(--color-base-700);
    --theme-elevation-350: var(--color-base-650);
    --theme-elevation-400: var(--color-base-600);
    --theme-elevation-450: var(--color-base-550);
    --theme-elevation-500: var(--color-base-500);
    --theme-elevation-550: var(--color-base-450);
    --theme-elevation-600: var(--color-base-400);
    --theme-elevation-650: var(--color-base-350);
    --theme-elevation-700: var(--color-base-300);
    --theme-elevation-750: var(--color-base-250);
    --theme-elevation-800: var(--color-base-200);
    --theme-elevation-850: var(--color-base-150);
    --theme-elevation-900: var(--color-base-100);
    --theme-elevation-950: var(--color-base-50);
    --theme-elevation-1000: var(--color-base-0);

    --theme-success-50: var(--color-success-950);
    --theme-success-100: var(--color-success-900);
    --theme-success-150: var(--color-success-850);
    --theme-success-200: var(--color-success-800);
    --theme-success-250: var(--color-success-750);
    --theme-success-300: var(--color-success-700);
    --theme-success-350: var(--color-success-650);
    --theme-success-400: var(--color-success-600);
    --theme-success-450: var(--color-success-550);
    --theme-success-500: var(--color-success-500);
    --theme-success-550: var(--color-success-450);
    --theme-success-600: var(--color-success-400);
    --theme-success-650: var(--color-success-350);
    --theme-success-700: var(--color-success-300);
    --theme-success-750: var(--color-success-250);
    --theme-success-800: var(--color-success-200);
    --theme-success-850: var(--color-success-150);
    --theme-success-900: var(--color-success-100);
    --theme-success-950: var(--color-success-50);

    --theme-warning-50: var(--color-warning-950);
    --theme-warning-100: var(--color-warning-900);
    --theme-warning-150: var(--color-warning-850);
    --theme-warning-200: var(--color-warning-800);
    --theme-warning-250: var(--color-warning-750);
    --theme-warning-300: var(--color-warning-700);
    --theme-warning-350: var(--color-warning-650);
    --theme-warning-400: var(--color-warning-600);
    --theme-warning-450: var(--color-warning-550);
    --theme-warning-500: var(--color-warning-500);
    --theme-warning-550: var(--color-warning-450);
    --theme-warning-600: var(--color-warning-400);
    --theme-warning-650: var(--color-warning-350);
    --theme-warning-700: var(--color-warning-300);
    --theme-warning-750: var(--color-warning-250);
    --theme-warning-800: var(--color-warning-200);
    --theme-warning-850: var(--color-warning-150);
    --theme-warning-900: var(--color-warning-100);
    --theme-warning-950: var(--color-warning-50);

    --theme-error-50: var(--color-error-950);
    --theme-error-100: var(--color-error-900);
    --theme-error-150: var(--color-error-850);
    --theme-error-200: var(--color-error-800);
    --theme-error-250: var(--color-error-750);
    --theme-error-300: var(--color-error-700);
    --theme-error-350: var(--color-error-650);
    --theme-error-400: var(--color-error-600);
    --theme-error-450: var(--color-error-550);
    --theme-error-500: var(--color-error-500);
    --theme-error-550: var(--color-error-450);
    --theme-error-600: var(--color-error-400);
    --theme-error-650: var(--color-error-350);
    --theme-error-700: var(--color-error-300);
    --theme-error-750: var(--color-error-250);
    --theme-error-800: var(--color-error-200);
    --theme-error-850: var(--color-error-150);
    --theme-error-900: var(--color-error-100);
    --theme-error-950: var(--color-error-50);

    --theme-bg: var(--theme-elevation-100);
    --theme-text: var(--theme-elevation-900);
    --theme-input-bg: var(--theme-elevation-150);
    --theme-border-color: var(--theme-elevation-250);

    color-scheme: dark;
    color: var(--theme-text);

    --highlight-default-bg-color: var(--theme-success-100);
    --highlight-default-text-color: var(--theme-success-600);

    --highlight-danger-bg-color: var(--theme-error-100);
    --highlight-danger-text-color: var(--theme-error-550);
  }

  h1 a,
  h2 a,
  h3 a,
  h4 a,
  h5 a,
  h6 a {
    color: var(--theme-success-600);

    &:hover {
      color: var(--theme-success-400);
    }

    &:visited {
      color: var(--theme-success-700);

      &:hover {
        color: var(--theme-success-500);
      }
    }
  }
}
````

## File: src/app/(app)/_css/type.scss
````scss
@use 'queries' as *;

%h1,
%h2,
%h3,
%h4,
%h5,
%h6 {
  font-weight: 700;
}

%h1 {
  margin: 40px 0;
  font-size: 64px;
  line-height: 70px;
  font-weight: bold;

  @include mid-break {
    margin: 24px 0;
    font-size: 42px;
    line-height: 42px;
  }
}

%h2 {
  margin: 28px 0;
  font-size: 48px;
  line-height: 54px;
  font-weight: bold;

  @include mid-break {
    margin: 22px 0;
    font-size: 32px;
    line-height: 40px;
  }
}

%h3 {
  margin: 24px 0;
  font-size: 32px;
  line-height: 40px;
  font-weight: bold;

  @include mid-break {
    margin: 20px 0;
    font-size: 26px;
    line-height: 32px;
  }
}

%h4 {
  margin: 20px 0;
  font-size: 26px;
  line-height: 32px;
  font-weight: bold;

  @include mid-break {
    font-size: 22px;
    line-height: 30px;
  }
}

%h5 {
  margin: 20px 0;
  font-size: 22px;
  line-height: 30px;
  font-weight: bold;

  @include mid-break {
    font-size: 18px;
    line-height: 24px;
  }
}

%h6 {
  margin: 20px 0;
  font-size: inherit;
  line-height: inherit;
  font-weight: bold;
}

%body {
  font-size: 18px;
  line-height: 32px;

  @include mid-break {
    font-size: 15px;
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
}

%label {
  font-size: 16px;
  line-height: 24px;
  text-transform: uppercase;

  @include mid-break {
    font-size: 13px;
  }
}
````

## File: src/app/(app)/_providers/Auth/gql.ts
````typescript
export const USER = `
  id
  email
  firstName
  lastName
`

// eslint-disable-next-line @typescript-eslint/no-explicit-any
export const gql = async (query: string): Promise<any> => {
  try {
    const res = await fetch(`${process.env.NEXT_PUBLIC_SERVER_URL}/api/graphql`, {
      body: JSON.stringify({
        query,
      }),
      credentials: 'include',
      headers: {
        'Content-Type': 'application/json',
      },
      method: 'POST',
    })

    const { data, errors } = await res.json()

    if (errors) {
      throw new Error(errors[0].message)
    }

    if (res.ok && data) {
      return data
    }
  } catch (e: unknown) {
    throw new Error(e as string)
  }
}
````

## File: src/app/(app)/_providers/Auth/index.tsx
````typescript
'use client'

import type { Permissions } from 'payload/auth'

import React, { createContext, useCallback, useContext, useEffect, useState } from 'react'

import type { User } from '../../../../payload-types'
import type { AuthContext, Create, ForgotPassword, Login, Logout, ResetPassword } from './types'

import { gql, USER } from './gql'
import { rest } from './rest'

const Context = createContext({} as AuthContext)

export const AuthProvider: React.FC<{ api?: 'gql' | 'rest'; children: React.ReactNode }> = ({
  api = 'rest',
  children,
}) => {
  const [user, setUser] = useState<null | User>()
  const [permissions, setPermissions] = useState<null | Permissions>(null)

  const create = useCallback<Create>(
    async (args) => {
      if (api === 'rest') {
        const user = await rest(`${process.env.NEXT_PUBLIC_SERVER_URL}/api/users`, args)
        setUser(user)
        return user
      }

      if (api === 'gql') {
        const { createUser: user } = await gql(`mutation {
        createUser(data: { email: "${args.email}", password: "${args.password}", firstName: "${args.firstName}", lastName: "${args.lastName}" }) {
          ${USER}
        }
      }`)

        setUser(user)
        return user
      }
    },
    [api],
  )

  const login = useCallback<Login>(
    async (args) => {
      if (api === 'rest') {
        const user = await rest(`${process.env.NEXT_PUBLIC_SERVER_URL}/api/users/login`, args)
        setUser(user)
        return user
      }

      if (api === 'gql') {
        const { loginUser } = await gql(`mutation {
        loginUser(email: "${args.email}", password: "${args.password}") {
          user {
            ${USER}
          }
          exp
        }
      }`)

        setUser(loginUser?.user)
        return loginUser?.user
      }
    },
    [api],
  )

  const logout = useCallback<Logout>(async () => {
    if (api === 'rest') {
      await rest(`${process.env.NEXT_PUBLIC_SERVER_URL}/api/users/logout`)
      setUser(null)
      return
    }

    if (api === 'gql') {
      await gql(`mutation {
        logoutUser
      }`)

      setUser(null)
    }
  }, [api])

  // On mount, get user and set
  useEffect(() => {
    const fetchMe = async () => {
      if (api === 'rest') {
        const user = await rest(
          `${process.env.NEXT_PUBLIC_SERVER_URL}/api/users/me`,
          {},
          {
            method: 'GET',
          },
        )
        setUser(user)
      }

      if (api === 'gql') {
        const { meUser } = await gql(`query {
          meUser {
            user {
              ${USER}
            }
            exp
          }
        }`)

        setUser(meUser.user)
      }
    }

    void fetchMe()
  }, [api])

  const forgotPassword = useCallback<ForgotPassword>(
    async (args) => {
      if (api === 'rest') {
        const user = await rest(
          `${process.env.NEXT_PUBLIC_SERVER_URL}/api/users/forgot-password`,
          args,
        )
        setUser(user)
        return user
      }

      if (api === 'gql') {
        const { forgotPasswordUser } = await gql(`mutation {
        forgotPasswordUser(email: "${args.email}")
      }`)

        return forgotPasswordUser
      }
    },
    [api],
  )

  const resetPassword = useCallback<ResetPassword>(
    async (args) => {
      if (api === 'rest') {
        const user = await rest(
          `${process.env.NEXT_PUBLIC_SERVER_URL}/api/users/reset-password`,
          args,
        )
        setUser(user)
        return user
      }

      if (api === 'gql') {
        const { resetPasswordUser } = await gql(`mutation {
        resetPasswordUser(password: "${args.password}", token: "${args.token}") {
          user {
            ${USER}
          }
        }
      }`)

        setUser(resetPasswordUser.user)
        return resetPasswordUser.user
      }
    },
    [api],
  )

  return (
    <Context.Provider
      value={{
        create,
        forgotPassword,
        login,
        logout,
        permissions,
        resetPassword,
        setPermissions,
        setUser,
        user,
      }}
    >
      {children}
    </Context.Provider>
  )
}

type UseAuth<T = User> = () => AuthContext  

export const useAuth: UseAuth = () => useContext(Context)
````

## File: src/app/(app)/_providers/Auth/rest.ts
````typescript
import type { User } from '../../../../payload-types'

export const rest = async (
  url: string,
  args?: any, // eslint-disable-line @typescript-eslint/no-explicit-any
  options?: RequestInit,
): Promise<null | undefined | User> => {
  const method = options?.method || 'POST'

  try {
    const res = await fetch(url, {
      method,
      ...(method === 'POST' ? { body: JSON.stringify(args) } : {}),
      credentials: 'include',
      headers: {
        'Content-Type': 'application/json',
        ...options?.headers,
      },
      ...options,
    })

    const { errors, user } = await res.json()

    if (errors) {
      throw new Error(errors[0].message)
    }

    if (res.ok) {
      return user
    }
  } catch (e: unknown) {
    throw new Error(e as string)
  }
}
````

## File: src/app/(app)/_providers/Auth/types.ts
````typescript
import type { Permissions } from 'payload/auth'

import type { User } from '../../../../payload-types'

 
export type ResetPassword = (args: {
  password: string
  passwordConfirm: string
  token: string
}) => Promise<User>

export type ForgotPassword = (args: { email: string }) => Promise<User>  

export type Create = (args: {
  email: string
  firstName: string
  lastName: string
  password: string
}) => Promise<User>  

export type Login = (args: { email: string; password: string }) => Promise<User>  

export type Logout = () => Promise<void>

export interface AuthContext {
  create: Create
  forgotPassword: ForgotPassword
  login: Login
  logout: Logout
  permissions?: null | Permissions
  resetPassword: ResetPassword
  setPermissions: (permissions: null | Permissions) => void
  setUser: (user: null | User) => void  
  user?: null | User
}
````

## File: src/app/(app)/account/AccountForm/index.module.scss
````scss
@import '../../_css/common';

.form {
  margin-bottom: var(--base);
  display: flex;
  flex-direction: column;
  gap: calc(var(--base) / 2);
  align-items: flex-start;
  width: 66.66%;

  @include mid-break {
    width: 100%;
  }
}

.changePassword {
  all: unset;
  cursor: pointer;
  text-decoration: underline;
}

.submit {
  margin-top: calc(var(--base) / 2);
}
````

## File: src/app/(app)/account/AccountForm/index.tsx
````typescript
'use client'

import { useRouter } from 'next/navigation'
import React, { Fragment, useCallback, useEffect, useRef, useState } from 'react'
import { useForm } from 'react-hook-form'

import { Button } from '../../_components/Button'
import { Input } from '../../_components/Input'
import { Message } from '../../_components/Message'
import { useAuth } from '../../_providers/Auth'
import classes from './index.module.scss'

type FormData = {
  email: string
  name: string
  password: string
  passwordConfirm: string
}

export const AccountForm: React.FC = () => {
  const [error, setError] = useState('')
  const [success, setSuccess] = useState('')
  const { setUser, user } = useAuth()
  const [changePassword, setChangePassword] = useState(false)
  const router = useRouter()

  const {
    formState: { errors, isLoading },
    handleSubmit,
    register,
    reset,
    watch,
  } = useForm<FormData>()

  const password = useRef({})
  password.current = watch('password', '')

  const onSubmit = useCallback(
    async (data: FormData) => {
      if (user) {
        const response = await fetch(`${process.env.NEXT_PUBLIC_SERVER_URL}/api/users/${user.id}`, {
          // Make sure to include cookies with fetch
          body: JSON.stringify(data),
          credentials: 'include',
          headers: {
            'Content-Type': 'application/json',
          },
          method: 'PATCH',
        })

        if (response.ok) {
          const json = await response.json()
          setUser(json.doc)
          setSuccess('Successfully updated account.')
          setError('')
          setChangePassword(false)
          reset({
            name: json.doc.name,
            email: json.doc.email,
            password: '',
            passwordConfirm: '',
          })
        } else {
          setError('There was a problem updating your account.')
        }
      }
    },
    [user, setUser, reset],
  )

  useEffect(() => {
    if (user === null) {
      router.push(`/login?unauthorized=account`)
    }

    // Once user is loaded, reset form to have default values
    if (user) {
      reset({
        email: user.email,
        password: '',
        passwordConfirm: '',
      })
    }
  }, [user, router, reset, changePassword])

  return (
    <form className={classes.form} onSubmit={handleSubmit(onSubmit)}>
      <Message className={classes.message} error={error} success={success} />
      {!changePassword ? (
        <Fragment>
          <p>
            {'To change your password, '}
            <button
              className={classes.changePassword}
              onClick={() => setChangePassword(!changePassword)}
              type="button"
            >
              click here
            </button>
            .
          </p>
          <Input
            error={errors.email}
            label="Email Address"
            name="email"
            register={register}
            required
            type="email"
          />
        </Fragment>
      ) : (
        <Fragment>
          <p>
            {'Change your password below, or '}
            <button
              className={classes.changePassword}
              onClick={() => setChangePassword(!changePassword)}
              type="button"
            >
              cancel
            </button>
            .
          </p>
          <Input
            error={errors.password}
            label="Password"
            name="password"
            register={register}
            required
            type="password"
          />
          <Input
            error={errors.passwordConfirm}
            label="Confirm Password"
            name="passwordConfirm"
            register={register}
            required
            type="password"
            validate={(value) => value === password.current || 'The passwords do not match'}
          />
        </Fragment>
      )}
      <Button
        appearance="primary"
        className={classes.submit}
        label={isLoading ? 'Processing' : changePassword ? 'Change password' : 'Update account'}
        type="submit"
      />
    </form>
  )
}
````

## File: src/app/(app)/account/index.module.scss
````scss
.account {
  margin-bottom: var(--block-padding);
}

.params {
  margin-top: var(--base);
}
````

## File: src/app/(app)/account/page.tsx
````typescript
import { headers as getHeaders } from 'next/headers.js'
import Link from 'next/link'
import { redirect } from 'next/navigation'
import { getPayload } from 'payload'
import React, { Fragment } from 'react'

import config from '../../../payload.config'
import { Button } from '../_components/Button'
import { Gutter } from '../_components/Gutter'
import { HydrateClientUser } from '../_components/HydrateClientUser'
import { RenderParams } from '../_components/RenderParams'
import { AccountForm } from './AccountForm'
import classes from './index.module.scss'

export default async function Account() {
  const headers = await getHeaders()
  const payload = await getPayload({ config })
  const { permissions, user } = await payload.auth({ headers })

  if (!user) {
    redirect(
      `/login?error=${encodeURIComponent('You must be logged in to access your account.')}&redirect=/account`,
    )
  }

  return (
    <Fragment>
      <HydrateClientUser permissions={permissions} user={user} />
      <Gutter className={classes.account}>
        <RenderParams className={classes.params} />
        <h1>Account</h1>
        <p>
          {`This is your account dashboard. Here you can update your account information and more. To manage all users, `}
          <Link href={`${process.env.NEXT_PUBLIC_SERVER_URL}/admin/collections/users`}>
            login to the admin dashboard
          </Link>
          .
        </p>
        <AccountForm />
        <Button appearance="secondary" href="/logout" label="Log out" />
      </Gutter>
    </Fragment>
  )
}
````

## File: src/app/(app)/create-account/CreateAccountForm/index.module.scss
````scss
@import '../../_css/common';

.form {
  margin-bottom: var(--base);
  display: flex;
  flex-direction: column;
  gap: calc(var(--base) / 2);
  align-items: flex-start;
  width: 66.66%;

  @include mid-break {
    width: 100%;
  }
}

.submit {
  margin-top: calc(var(--base) / 2);
}

.message {
  margin-bottom: var(--base);
}
````

## File: src/app/(app)/create-account/CreateAccountForm/index.tsx
````typescript
'use client'

import Link from 'next/link'
import { useRouter, useSearchParams } from 'next/navigation'
import React, { useCallback, useRef, useState } from 'react'
import { useForm } from 'react-hook-form'

import { Button } from '../../_components/Button'
import { Input } from '../../_components/Input'
import { Message } from '../../_components/Message'
import { useAuth } from '../../_providers/Auth'
import classes from './index.module.scss'

type FormData = {
  email: string
  password: string
  passwordConfirm: string
}

export const CreateAccountForm: React.FC = () => {
  const searchParams = useSearchParams()
  const allParams = searchParams.toString() ? `?${searchParams.toString()}` : ''
  const { login } = useAuth()
  const router = useRouter()
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState<null | string>(null)

  const {
    formState: { errors },
    handleSubmit,
    register,
    watch,
  } = useForm<FormData>()

  const password = useRef({})
  password.current = watch('password', '')

  const onSubmit = useCallback(
    async (data: FormData) => {
      const response = await fetch(`${process.env.NEXT_PUBLIC_SERVER_URL}/api/users`, {
        body: JSON.stringify(data),
        headers: {
          'Content-Type': 'application/json',
        },
        method: 'POST',
      })

      if (!response.ok) {
        const message = response.statusText || 'There was an error creating the account.'
        setError(message)
        return
      }

      const redirect = searchParams.get('redirect')

      const timer = setTimeout(() => {
        setLoading(true)
      }, 1000)

      try {
        await login(data)
        clearTimeout(timer)
        if (redirect) {router.push(redirect)}
        else {router.push(`/account?success=${encodeURIComponent('Account created successfully')}`)}
      } catch (_) {
        clearTimeout(timer)
        setError('There was an error with the credentials provided. Please try again.')
      }
    },
    [login, router, searchParams],
  )

  return (
    <form className={classes.form} onSubmit={handleSubmit(onSubmit)}>
      <p>
        {`This is where new customers can signup and create a new account. To manage all users, `}
        <Link href={`${process.env.NEXT_PUBLIC_SERVER_URL}/admin/collections/users`}>
          login to the admin dashboard
        </Link>
        .
      </p>
      <Message className={classes.message} error={error} />
      <Input
        error={errors.email}
        label="Email Address"
        name="email"
        register={register}
        required
        type="email"
      />
      <Input
        error={errors.password}
        label="Password"
        name="password"
        register={register}
        required
        type="password"
      />
      <Input
        error={errors.passwordConfirm}
        label="Confirm Password"
        name="passwordConfirm"
        register={register}
        required
        type="password"
        validate={(value) => value === password.current || 'The passwords do not match'}
      />
      <Button
        appearance="primary"
        className={classes.submit}
        label={loading ? 'Processing' : 'Create Account'}
        type="submit"
      />
      <div>
        {'Already have an account? '}
        <Link href={`/login${allParams}`}>Login</Link>
      </div>
    </form>
  )
}
````

## File: src/app/(app)/create-account/index.module.scss
````scss
@import '../_css/common';

.createAccount {
  margin-bottom: var(--block-padding);
}
````

## File: src/app/(app)/create-account/page.tsx
````typescript
import { headers as getHeaders } from 'next/headers.js'
import { redirect } from 'next/navigation'
import { getPayload } from 'payload'
import React from 'react'

import config from '../../../payload.config'
import { Gutter } from '../_components/Gutter'
import { RenderParams } from '../_components/RenderParams'
import { CreateAccountForm } from './CreateAccountForm'
import classes from './index.module.scss'

export default async function CreateAccount() {
  const headers = await getHeaders()
  const payload = await getPayload({ config })
  const { user } = await payload.auth({ headers })

  if (user) {
    redirect(
      `/account?message=${encodeURIComponent(
        'Cannot create a new account while logged in, please log out and try again.',
      )}`,
    )
  }

  return (
    <Gutter className={classes.createAccount}>
      <h1>Create Account</h1>
      <RenderParams />
      <CreateAccountForm />
    </Gutter>
  )
}
````

## File: src/app/(app)/login/LoginForm/index.module.scss
````scss
@import '../../_css/common';

.form {
  margin-bottom: var(--base);
  display: flex;
  flex-direction: column;
  gap: calc(var(--base) / 2);
  align-items: flex-start;
  width: 66.66%;

  @include mid-break {
    width: 100%;
  }
}

.submit {
  margin-top: calc(var(--base) / 2);
}

.message {
  margin-bottom: var(--base);
}
````

## File: src/app/(app)/login/LoginForm/index.tsx
````typescript
'use client'

import Link from 'next/link'
import { useRouter, useSearchParams } from 'next/navigation'
import React, { useCallback, useRef } from 'react'
import { useForm } from 'react-hook-form'

import { Button } from '../../_components/Button'
import { Input } from '../../_components/Input'
import { Message } from '../../_components/Message'
import { useAuth } from '../../_providers/Auth'
import classes from './index.module.scss'

type FormData = {
  email: string
  password: string
}

export const LoginForm: React.FC = () => {
  const searchParams = useSearchParams()
  const allParams = searchParams.toString() ? `?${searchParams.toString()}` : ''
  const redirect = useRef(searchParams.get('redirect'))
  const { login } = useAuth()
  const router = useRouter()
  const [error, setError] = React.useState<null | string>(null)

  const {
    formState: { errors, isLoading },
    handleSubmit,
    register,
  } = useForm<FormData>({
    defaultValues: {
      email: 'demo@payloadcms.com',
      password: 'demo',
    },
  })

  const onSubmit = useCallback(
    async (data: FormData) => {
      try {
        await login(data)
        if (redirect?.current) {router.push(redirect.current)}
        else {router.push('/account')}
      } catch (_) {
        setError('There was an error with the credentials provided. Please try again.')
      }
    },
    [login, router],
  )

  return (
    <form className={classes.form} onSubmit={handleSubmit(onSubmit)}>
      <p>
        {'To log in, use the email '}
        <b>demo@payloadcms.com</b>
        {' with the password '}
        <b>demo</b>
        {'. To manage your users, '}
        <Link href={`${process.env.NEXT_PUBLIC_SERVER_URL}/admin/collections/users`}>
          login to the admin dashboard
        </Link>
        .
      </p>
      <Message className={classes.message} error={error} />
      <Input
        error={errors.email}
        label="Email Address"
        name="email"
        register={register}
        required
        type="email"
      />
      <Input
        error={errors.password}
        label="Password"
        name="password"
        register={register}
        required
        type="password"
      />
      <Button
        appearance="primary"
        className={classes.submit}
        disabled={isLoading}
        label={isLoading ? 'Processing' : 'Login'}
        type="submit"
      />
      <div>
        <Link href={`/create-account${allParams}`}>Create an account</Link>
        <br />
        <Link href={`/recover-password${allParams}`}>Recover your password</Link>
      </div>
    </form>
  )
}
````

## File: src/app/(app)/login/index.module.scss
````scss
@import '../_css/common';

.login {
  margin-bottom: var(--block-padding);
}

.params {
  margin-top: var(--base);
}
````

## File: src/app/(app)/login/page.tsx
````typescript
import { headers as getHeaders } from 'next/headers.js'
import { redirect } from 'next/navigation'
import { getPayload } from 'payload'
import React from 'react'

import config from '../../../payload.config'
import { Gutter } from '../_components/Gutter'
import { RenderParams } from '../_components/RenderParams'
import classes from './index.module.scss'
import { LoginForm } from './LoginForm'

export default async function Login() {
  const headers = await getHeaders()
  const payload = await getPayload({ config })
  const { user } = await payload.auth({ headers })

  if (user) {
    redirect(`/account?message=${encodeURIComponent('You are already logged in.')}`)
  }

  return (
    <Gutter className={classes.login}>
      <RenderParams className={classes.params} />
      <h1>Log in</h1>
      <LoginForm />
    </Gutter>
  )
}
````

## File: src/app/(app)/logout/LogoutPage/index.tsx
````typescript
'use client'

import Link from 'next/link'
import React, { Fragment, useEffect, useState } from 'react'

import { useAuth } from '../../_providers/Auth'

export const LogoutPage: React.FC = () => {
  const { logout } = useAuth()
  const [success, setSuccess] = useState('')
  const [error, setError] = useState('')

  useEffect(() => {
    const performLogout = async () => {
      try {
        await logout()
        setSuccess('Logged out successfully.')
      } catch (_) {
        setError('You are already logged out.')
      }
    }

    void performLogout()
  }, [logout])

  return (
    <Fragment>
      {(error || success) && (
        <div>
          <h1>{error || success}</h1>
          <p>
            {'What would you like to do next? '}
            <Link href="/">Click here</Link>
            {` to go to the home page. To log back in, `}
            <Link href="/login">click here</Link>.
          </p>
        </div>
      )}
    </Fragment>
  )
}
````

## File: src/app/(app)/logout/index.module.scss
````scss
.logout {
  margin-bottom: var(--block-padding);
}
````

## File: src/app/(app)/logout/page.tsx
````typescript
import { headers as getHeaders } from 'next/headers.js'
import Link from 'next/link'
import { getPayload } from 'payload'
import React from 'react'

import config from '../../../payload.config'
import { Gutter } from '../_components/Gutter'
import classes from './index.module.scss'
import { LogoutPage } from './LogoutPage'

export default async function Logout() {
  const headers = await getHeaders()
  const payload = await getPayload({ config })
  const { user } = await payload.auth({ headers })

  if (!user) {
    return (
      <Gutter className={classes.logout}>
        <h1>You are already logged out.</h1>
        <p>
          {'What would you like to do next? '}
          <Link href="/">Click here</Link>
          {` to go to the home page. To log back in, `}
          <Link href="/login">click here</Link>.
        </p>
      </Gutter>
    )
  }

  return (
    <Gutter className={classes.logout}>
      <LogoutPage />
    </Gutter>
  )
}
````

## File: src/app/(app)/recover-password/RecoverPasswordForm/index.module.scss
````scss
@import '../../_css/common';

.error {
  color: red;
  margin-bottom: 15px;
}

.formWrapper {
  width: 66.66%;

  @include mid-break {
    width: 100%;
  }
}

.submit {
  margin-top: var(--base);
}

.message {
  margin-bottom: var(--base);
}
````

## File: src/app/(app)/recover-password/RecoverPasswordForm/index.tsx
````typescript
'use client'

import Link from 'next/link'
import React, { Fragment, useCallback, useState } from 'react'
import { useForm } from 'react-hook-form'

import { Button } from '../../_components/Button'
import { Input } from '../../_components/Input'
import { Message } from '../../_components/Message'
import classes from './index.module.scss'

type FormData = {
  email: string
}

export const RecoverPasswordForm: React.FC = () => {
  const [error, setError] = useState('')
  const [success, setSuccess] = useState(false)

  const {
    formState: { errors },
    handleSubmit,
    register,
  } = useForm<FormData>()

  const onSubmit = useCallback(async (data: FormData) => {
    const response = await fetch(
      `${process.env.NEXT_PUBLIC_SERVER_URL}/api/users/forgot-password`,
      {
        body: JSON.stringify(data),
        headers: {
          'Content-Type': 'application/json',
        },
        method: 'POST',
      },
    )

    if (response.ok) {
      setSuccess(true)
      setError('')
    } else {
      setError(
        'There was a problem while attempting to send you a password reset email. Please try again.',
      )
    }
  }, [])

  return (
    <Fragment>
      {!success && (
        <React.Fragment>
          <h1>Recover Password</h1>
          <div className={classes.formWrapper}>
            <p>
              {`Please enter your email below. You will receive an email message with instructions on
              how to reset your password. To manage all of your users, `}
              <Link href={`${process.env.NEXT_PUBLIC_SERVER_URL}/admin/collections/users`}>
                login to the admin dashboard
              </Link>
              .
            </p>
            <form className={classes.form} onSubmit={handleSubmit(onSubmit)}>
              <Message className={classes.message} error={error} />
              <Input
                error={errors.email}
                label="Email Address"
                name="email"
                register={register}
                required
                type="email"
              />
              <Button
                appearance="primary"
                className={classes.submit}
                label="Recover Password"
                type="submit"
              />
            </form>
          </div>
        </React.Fragment>
      )}
      {success && (
        <React.Fragment>
          <h1>Request submitted</h1>
          <p>Check your email for a link that will allow you to securely reset your password.</p>
        </React.Fragment>
      )}
    </Fragment>
  )
}
````

## File: src/app/(app)/recover-password/index.module.scss
````scss
@import '../_css/common';

.recoverPassword {
  margin-bottom: var(--block-padding);
}
````

## File: src/app/(app)/recover-password/page.tsx
````typescript
import { headers as getHeaders } from 'next/headers.js'
import { redirect } from 'next/navigation'
import { getPayload } from 'payload'
import React from 'react'

import config from '../../../payload.config'
import { Gutter } from '../_components/Gutter'
import classes from './index.module.scss'
import { RecoverPasswordForm } from './RecoverPasswordForm'

export default async function RecoverPassword() {
  const headers = await getHeaders()
  const payload = await getPayload({ config })
  const { user } = await payload.auth({ headers })

  if (user) {
    redirect(`/account?message=${encodeURIComponent('Cannot recover password while logged in.')}`)
  }

  return (
    <Gutter className={classes.recoverPassword}>
      <RecoverPasswordForm />
    </Gutter>
  )
}
````

## File: src/app/(app)/reset-password/ResetPasswordForm/index.module.scss
````scss
@import '../../_css/common';

.form {
  width: 66.66%;

  @include mid-break {
    width: 100%;
  }
}

.submit {
  margin-top: var(--base);
}
````

## File: src/app/(app)/reset-password/ResetPasswordForm/index.tsx
````typescript
'use client'

import { useRouter, useSearchParams } from 'next/navigation'
import React, { useCallback, useEffect, useState } from 'react'
import { useForm } from 'react-hook-form'

import { Button } from '../../_components/Button'
import { Input } from '../../_components/Input'
import { Message } from '../../_components/Message'
import { useAuth } from '../../_providers/Auth'
import classes from './index.module.scss'

type FormData = {
  password: string
  token: string
}

export const ResetPasswordForm: React.FC = () => {
  const [error, setError] = useState('')
  const { login } = useAuth()
  const router = useRouter()
  const searchParams = useSearchParams()
  const token = searchParams.get('token')

  const {
    formState: { errors },
    handleSubmit,
    register,
    reset,
  } = useForm<FormData>()

  const onSubmit = useCallback(
    async (data: FormData) => {
      const response = await fetch(
        `${process.env.NEXT_PUBLIC_SERVER_URL}/api/users/reset-password`,
        {
          body: JSON.stringify(data),
          headers: {
            'Content-Type': 'application/json',
          },
          method: 'POST',
        },
      )

      if (response.ok) {
        const json = await response.json()

        // Automatically log the user in after they successfully reset password
        await login({ email: json.user.email, password: data.password })

        // Redirect them to `/account` with success message in URL
        router.push('/account?success=Password reset successfully.')
      } else {
        setError('There was a problem while resetting your password. Please try again later.')
      }
    },
    [router, login],
  )

  // when Next.js populates token within router,
  // reset form with new token value
  useEffect(() => {
    reset({ token: token || undefined })
  }, [reset, token])

  return (
    <form className={classes.form} onSubmit={handleSubmit(onSubmit)}>
      <Message className={classes.message} error={error} />
      <Input
        error={errors.password}
        label="New Password"
        name="password"
        register={register}
        required
        type="password"
      />
      <input type="hidden" {...register('token')} />
      <Button
        appearance="primary"
        className={classes.submit}
        label="Reset Password"
        type="submit"
      />
    </form>
  )
}
````

## File: src/app/(app)/reset-password/index.module.scss
````scss
.resetPassword {
  margin-bottom: var(--block-padding);
}
````

## File: src/app/(app)/reset-password/page.tsx
````typescript
import { headers as getHeaders } from 'next/headers.js'
import { redirect } from 'next/navigation'
import { getPayload } from 'payload'
import React from 'react'

import config from '../../../payload.config'
import { Gutter } from '../_components/Gutter'
import classes from './index.module.scss'
import { ResetPasswordForm } from './ResetPasswordForm'

export default async function ResetPassword() {
  const headers = await getHeaders()
  const payload = await getPayload({ config })
  const { user } = await payload.auth({ headers })

  if (user) {
    redirect(`/account?message=${encodeURIComponent('Cannot reset password while logged in.')}`)
  }

  return (
    <Gutter className={classes.resetPassword}>
      <h1>Reset Password</h1>
      <p>Please enter a new password below.</p>
      <ResetPasswordForm />
    </Gutter>
  )
}
````

## File: src/app/(app)/layout.tsx
````typescript
import React from 'react'

import { Header } from './_components/Header'
import './_css/app.scss'
import { AuthProvider } from './_providers/Auth'

export const metadata = {
  description: 'An example of how to authenticate with Payload from a Next.js app.',
  title: 'Payload Auth + Next.js App Router Example',
}

export default function RootLayout(props: { children: React.ReactNode }) {
  const { children } = props

  return (
    <html lang="en">
      <body>
        <AuthProvider
          // To toggle between the REST and GraphQL APIs,
          // change the `api` prop to either `rest` or `gql`
          api="rest" // change this to `gql` to use the GraphQL API
        >
          <Header />
          <main>{children}</main>
        </AuthProvider>
      </body>
    </html>
  )
}
````

## File: src/app/(app)/page.tsx
````typescript
import { headers as getHeaders } from 'next/headers.js'
import Link from 'next/link'
import { getPayload } from 'payload'
import React, { Fragment } from 'react'

import config from '../../payload.config'
import { Gutter } from './_components/Gutter'
import { HydrateClientUser } from './_components/HydrateClientUser'

export default async function HomePage() {
  const headers = await getHeaders()
  const payload = await getPayload({ config })
  const { permissions, user } = await payload.auth({ headers })

  return (
    <Fragment>
      <HydrateClientUser permissions={permissions} user={user} />
      <Gutter>
        <h1>Payload Auth Example</h1>
        <p>
          {'This is a '}
          <Link href="https://payloadcms.com" rel="noopener noreferrer" target="_blank">
            Payload
          </Link>
          {' + '}
          <Link href="https://nextjs.org" rel="noopener noreferrer" target="_blank">
            Next.js
          </Link>
          {' app using the '}
          <Link href="https://nextjs.org/docs/app" rel="noopener noreferrer" target="_blank">
            App Router
          </Link>
          {' made explicitly for the '}
          <Link href="https://github.com/payloadcms/payload/tree/main/examples/auth">
            Payload Auth Example
          </Link>
          {". This example demonstrates how to implement Payload's "}
          <Link href="https://payloadcms.com/docs/authentication/overview">Authentication</Link>
          {
            ' strategies using the Local API, as well as through HTTP via the REST and GraphQL APIs. To toggle between these two HTTP APIs, see `_layout.tsx`.'
          }
        </p>
        <p>
          {'Visit the '}
          <Link href="/login">login page</Link>
          {' to start the authentication flow. Once logged in, you will be redirected to the '}
          <Link href="/account">account page</Link>
          {` which is restricted to users only. To manage all users, `}
          <Link href={`${process.env.NEXT_PUBLIC_SERVER_URL}/admin/collections/users`}>
            login to the admin dashboard
          </Link>
          .
        </p>
      </Gutter>
    </Fragment>
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
import { BeforeLogin as BeforeLogin_8a7ab0eb7ab5c511aba12e68480bfe5e } from '@/components/BeforeLogin'

export const importMap = {
  '@/components/BeforeLogin#BeforeLogin': BeforeLogin_8a7ab0eb7ab5c511aba12e68480bfe5e,
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

import '@payloadcms/next/css'
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

## File: src/collections/access/admins.ts
````typescript
import type { Access } from 'payload/config'

import { checkRole } from './checkRole'

export const admins: Access = ({ req: { user } }) => checkRole(['admin'], user)
````

## File: src/collections/access/adminsAndUser.ts
````typescript
import type { Access } from 'payload/config'

import { checkRole } from './checkRole'

const adminsAndUser: Access = ({ req: { user } }) => {
  if (user) {
    if (checkRole(['admin'], user)) {
      return true
    }

    return {
      id: user.id,
    }
  }

  return false
}

export default adminsAndUser
````

## File: src/collections/access/anyone.ts
````typescript
import type { Access } from 'payload/config'

export const anyone: Access = () => true
````

## File: src/collections/access/checkRole.ts
````typescript
import type { User } from '../../payload-types'

export const checkRole = (allRoles: User['roles'] = [], user: User = undefined): boolean => {
  if (user) {
    if (
      allRoles.some((role) => {
        return user?.roles?.some((individualRole) => {
          return individualRole === role
        })
      })
    )
      {return true}
  }

  return false
}
````

## File: src/collections/hooks/loginAfterCreate.ts
````typescript
import type { AfterChangeHook } from 'payload/dist/collections/config/types'

export const loginAfterCreate: AfterChangeHook = async ({
  doc,
  operation,
  req,
  req: { body = {}, payload, res },
}) => {
  if (operation === 'create') {
    const { email, password } = body

    if (email && password) {
      const { token, user } = await payload.login({
        collection: 'users',
        data: { email, password },
        req,
        res,
      })

      return {
        ...doc,
        token,
        user,
      }
    }
  }

  return doc
}
````

## File: src/collections/hooks/protectRoles.ts
````typescript
import type { FieldHook } from 'payload/types'

import type { User } from '../../payload-types'

// ensure there is always a `user` role
// do not let non-admins change roles
export const protectRoles: FieldHook<{ id: string } & User> = ({ data, req }) => {
  const isAdmin = req.user?.roles.includes('admin') || data.email === 'demo@payloadcms.com' // for the seed script

  if (!isAdmin) {
    return ['user']
  }

  const userRoles = new Set(data?.roles || [])
  userRoles.add('user')
  return [...userRoles]
}
````

## File: src/collections/Users.ts
````typescript
import type { CollectionConfig } from 'payload/types'

import { admins } from './access/admins'
import adminsAndUser from './access/adminsAndUser'
import { anyone } from './access/anyone'
import { checkRole } from './access/checkRole'
import { loginAfterCreate } from './hooks/loginAfterCreate'
import { protectRoles } from './hooks/protectRoles'

export const Users: CollectionConfig = {
  slug: 'users',
  auth: {
    tokenExpiration: 28800, // 8 hours
    cookies: {
      sameSite: 'none',
      secure: true,
      domain: process.env.COOKIE_DOMAIN,
    },
  },
  admin: {
    useAsTitle: 'email',
  },
  access: {
    read: adminsAndUser,
    create: anyone,
    update: adminsAndUser,
    delete: admins,
    admin: ({ req: { user } }) => checkRole(['admin'], user),
  },
  hooks: {
    afterChange: [loginAfterCreate],
  },
  fields: [
    {
      name: 'firstName',
      type: 'text',
    },
    {
      name: 'lastName',
      type: 'text',
    },
    {
      name: 'roles',
      type: 'select',
      hasMany: true,
      saveToJWT: true,
      hooks: {
        beforeChange: [protectRoles],
      },
      options: [
        {
          label: 'Admin',
          value: 'admin',
        },
        {
          label: 'User',
          value: 'user',
        },
      ],
    },
  ],
}
````

## File: src/components/BeforeLogin/index.tsx
````typescript
import React from 'react'

export const BeforeLogin: React.FC = () => {
  if (process.env.PAYLOAD_PUBLIC_SEED === 'true') {
    return (
      <p>
        {'Log in with the email '}
        <strong>demo@payloadcms.com</strong>
        {' and the password '}
        <strong>demo</strong>.
      </p>
    )
  }
  return null
}
````

## File: src/migrations/seed.ts
````typescript
import type { MigrateUpArgs } from '@payloadcms/db-mongodb'

export async function up({ payload }: MigrateUpArgs): Promise<void> {
  await payload.create({
    collection: 'users',
    data: {
      email: 'demo@payloadcms.com',
      password: 'demo',
      roles: ['admin'],
    },
  })
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
    users: User;
    'payload-locked-documents': PayloadLockedDocument;
    'payload-preferences': PayloadPreference;
    'payload-migrations': PayloadMigration;
  };
  collectionsJoins: {};
  collectionsSelect: {
    users: UsersSelect<false> | UsersSelect<true>;
    'payload-locked-documents': PayloadLockedDocumentsSelect<false> | PayloadLockedDocumentsSelect<true>;
    'payload-preferences': PayloadPreferencesSelect<false> | PayloadPreferencesSelect<true>;
    'payload-migrations': PayloadMigrationsSelect<false> | PayloadMigrationsSelect<true>;
  };
  db: {
    defaultIDType: string;
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
  id: string;
  firstName?: string | null;
  lastName?: string | null;
  roles?: ('admin' | 'user')[] | null;
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
  document?: {
    relationTo: 'users';
    value: string | User;
  } | null;
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
 * via the `definition` "users_select".
 */
export interface UsersSelect<T extends boolean = true> {
  firstName?: T;
  lastName?: T;
  roles?: T;
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

import { Users } from './collections/Users'
const filename = fileURLToPath(import.meta.url)
const dirname = path.dirname(filename)

export default buildConfig({
  admin: {
    components: {
      beforeLogin: ['@/components/BeforeLogin#BeforeLogin'],
    },
  },
  collections: [Users],
  cors: [process.env.NEXT_PUBLIC_SERVER_URL || ''].filter(Boolean),
  csrf: [process.env.NEXT_PUBLIC_SERVER_URL || ''].filter(Boolean),
  db: mongooseAdapter({
    url: process.env.DATABASE_URI || '',
  }),
  editor: slateEditor({}),
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

# Used to share cookies across subdomains
COOKIE_DOMAIN=localhost
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
}

export default withPayload(nextConfig)
````

## File: package.json
````json
{
  "name": "payload-example-auth",
  "version": "1.0.0",
  "description": "Payload authentication example.",
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
    "@payloadcms/db-mongodb": "latest",
    "@payloadcms/next": "latest",
    "@payloadcms/richtext-slate": "latest",
    "@payloadcms/ui": "latest",
    "cross-env": "^7.0.3",
    "next": "^15.0.0",
    "payload": "latest",
    "react": "19.0.0",
    "react-dom": "19.0.0",
    "react-hook-form": "^7.51.3"
  },
  "devDependencies": {
    "@payloadcms/graphql": "latest",
    "@swc/core": "^1.6.13",
    "@types/ejs": "^3.1.5",
    "@types/react": "19.0.1",
    "@types/react-dom": "19.0.1",
    "eslint": "^8.57.0",
    "eslint-config-next": "^15.0.0",
    "tsx": "^4.16.2",
    "typescript": "5.5.2"
  }
}
````

## File: README.md
````markdown
# Payload Auth Example

This [Payload Auth Example](https://github.com/payloadcms/payload/tree/main/examples/auth) demonstrates how to implement [Payload Authentication](https://payloadcms.com/docs/authentication/overview) into all types of applications. Follow the [Quick Start](#quick-start) to get up and running quickly.

## Quick Start

To spin up this example locally, follow the steps below:

1. Run the following command to create a project from the example:

- `npx create-payload-app --example auth`

2. Start the server:
   - Depending on your package manager, run `pnpm dev`, `yarn dev` or `npm run dev`
   - When prompted, type `y` then `enter` to seed the database with sample data
3. Access the application:
   - Open your browser and navigate to `http://localhost:3000` to access the homepage.
   - Open `http://localhost:3000/admin` to access the admin panel.
4. Login:

- Use the following credentials to log into the admin panel:
  > `Email: demo@payloadcms.com` > `Password: demo`

## How it works

### Collections

See the [Collections](https://payloadcms.com/docs/configuration/collections) docs for details on how to extend this functionality.

- #### Users (Authentication)

  Users are auth-enabled and encompass both admins and regular users based on the value of their `roles` field. Only `admin` users can access your admin panel to manage your content whereas `user` can authenticate on your front-end and access-controlled interfaces. See [Access Control](#access-control) for more details.

  **Local API**

  On the server, Payload provides all operations needed to authenticate users server-side using the Local API. In Next.js that might look something like this:

  ```ts
    import { headers as getHeaders } from 'next/headers.js'
    import { getPayload } from 'payload'
    import config from '../../payload.config'

    export default async function AccountPage({ searchParams }) {
      const headers = await getHeaders()
      const payload = await getPayload({ config: configPromise })
      const { permissions, user } = await payload.auth({ headers })

      if (!user) {
        redirect(
          `/login?error=${encodeURIComponent('You must be logged in to access your account.')}&redirect=/account`,
        )
      }

      return ...
    }
  ```

  **HTTP**

  The `users` collection also opens an http-layer to expose all [auth-related operations](https://payloadcms.com/docs/authentication/operations) through the REST and GraphQL APIs, including:

  - `Me`
  - `Login`
  - `Logout`
  - `Refresh Token`
  - `Verify Email`
  - `Unlock`
  - `Forgot Password`
  - `Reset Password`

  This might look something like this:

  ```ts
  await fetch('/api/users/me', {
    method: 'GET',
    credentials: 'include',
    headers: {
      'Content-Type': 'application/json',
    },
  })
  ```

  > NOTE: You can still use the HTTP APIs on the server if you don't have access to the Local API.

### Security

The [`cors`](https://payloadcms.com/docs/production/preventing-abuse#cross-origin-resource-sharing-cors), [`csrf`](https://payloadcms.com/docs/production/preventing-abuse#cross-site-request-forgery-csrf), and [`cookies`](https://payloadcms.com/docs/authentication/overview#options) settings are all configured to ensure that the admin panel and front-end can communicate with each other securely.

For additional help, see the [Authentication](https://payloadcms.com/docs/authentication/overview#authentication-overview) docs.

### Access Control

Basic role-based access control is setup to determine what users can and cannot do based on their roles, which are:

- `admin`: They can access the Payload admin panel to manage your application. They can see all data and make all operations.
- `user`: They cannot access the Payload admin panel and have a limited access to operations based on their user.

A `beforeChange` field hook called `protectRoles` is placed on this to automatically populate `roles` with the `user` role when a new user is created. It also protects roles from being changed by non-admins.

## Development

To spin up this example locally, follow the [Quick Start](#quick-start).

### Seed

On boot, a seed migration performed to create a user with email `demo@payloadcms.com`, password `demo`, the role `admin`.

> NOTICE: seeding the database is destructive because it drops your current database to populate a fresh one from the seed template. Only run this command if you are starting a new project or can afford to lose your current data.

## Production

To run Payload in production, you need to build and serve the Admin panel. To do so, follow these steps:

1. First invoke the `payload build` script by running `pnpm build`, `yarn build`, or `npm run build` in your project root. This creates a `./build` directory with a production-ready admin bundle.
1. Then run `pnpm serve`, `yarn serve`, or `npm run serve` to run Node.js in production and serve Payload from the `./build` directory.

### Deployment

If you are using an integrated Next.js setup, the easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new) from the creators of Next.js. Otherwise, easiest way to deploy your project is to use [Payload Cloud](https://payloadcms.com/new/import), a one-click hosting solution to deploy production-ready instances of your Payload apps directly from your GitHub repo. You can also deploy your app manually, check out the [deployment documentation](https://payloadcms.com/docs/production/deployment) for full details.

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
    ".next/types/**/*.ts"
  ],
  "exclude": [
    "node_modules"
  ]
}
````
