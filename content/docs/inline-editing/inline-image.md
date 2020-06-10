---
title: Inline Image
prev: /docs/inline-editing/inline-wysiwyg
next: /docs/inline-editing/inline-group
consumes:
  - file: /packages/react-tinacms-inline/src/inline-field-image.tsx
    description: Shows InlineImage
  - file: /packages/react-tinacms-inline/src/inline-field.tsx
    description: Depends on InlineField
---

The `InlineImage` field represents an image input. This field supports drag and drop upload, or via clicking on the image to select media from the local filesystem.

## Definition

Below is a simple example of how `InlineImage` could be implemented in in an [Inline Form](/docs/inline-editing).

```jsx
import ReactMarkdown from 'react-markdown'
import { useLocalForm } from 'tinacms'
import { InlineForm, InlineImage } from 'react-tinacms-inline'

// Example 'Page' Component
export function Page(props) {
  const [data, form] = useLocalForm(props.data)
  return (
    <InlineForm form={form}>
      <InlineImage
        name="hero_image"
        parse={filename => `/images/${filename}`}
        uploadDir={() => '/public/images/'}
        previewSrc={formValues => {}}
      />
    </InlineForm>
  )
}
```

There are two ways to use `InlineImage`, with and without children. If no children are passed, `InlineImage` will render a default `img` element. However, you may want more control over the image behavior, in which case you can [**pass children**](/docs/inline-editing/inline-image#example) to `InlineImage`.

## Options

| Key          | Description                                                                                                                                                                                                 |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`       | The path to some value in the data being edited.                                                                                                                                                            |
| `parse`      | Defines how the actual front matter or data value gets populated. The name of the file gets passed as an argument, and one can set the path this image as defined by the uploadDir property.                |
| `uploadDir`  | Defines the upload directory for the image. All of the post data is passed in, `fileRelativePath` is most useful in defining the upload directory, but you can also statically define the upload directory. |
| `previewSrc` | Defines the path for the src attribute on the image preview. If using gatsby-image, the path to the `childImageSharp.fluid.src` needs to be provided.                                                       |
| `focusRing`  | Controls whether to display a focus outline.                                                                                                                                                                |
| `children`   | Children need to be passed through the [Render Props](https://reactjs.org/docs/render-props.html) pattern to access `previewSrc`.                                                                           |

---

## Interface

```typescript
interface InlineImageProps {
  name: string
  parse(filename: string): string
  uploadDir(form: Form): string
  previewSrc(formValues: any): string
  focusRing?: boolean
  children?: any
}
```

## Example

Below is an example of how you could **pass children** as a to `InlineImage` to work with _Gatsby Image_. Notice how _children_ **need to be passed via** [**render props**](https://reactjs.org/docs/render-props.html). Read more on [proper image paths](/docs/fields/image#proper-image-paths-in-gatsby) in Gatsby to get context on the `parse` & `uploadDir` configuration.

```jsx
import {
  Inlineform,
  InlineImage,
  InlineTextareaField,
} from 'react-tinacms-inline'
import { useRemarkForm } from 'gatsby-tinacms-remark'
import { usePlugin } from 'tinacms'
import Img from 'gatsby-image'

// Using InlineImage with Gatsby Image
export function Hero({ data }) {
  const [post, form] = useRemarkForm(data.markdownRemark)

  usePlugin(form)

  return (
    <InlineForm form={form}>
      <InlineImage
        name="rawFrontmatter.thumbnail"
        parse={filename => (filename ? `./${filename}` : null)}
        uploadDir={blogPost => {
          const postPathParts = blogPost.initialValues.fileRelativePath.split(
            '/'
          )

          const postDirectory = postPathParts
            .splice(0, postPathParts.length - 1)
            .join('/')

          return postDirectory
        }}
        previewSrc={formValues => {
          const preview =
            formValues.frontmatter.thumbnail.childImageSharp.fluid.src
          return preview
        }}
      >
        {props => (
          <Img
            fluid={post.frontmatter.thumbnail.childImageSharp.fluid}
            alt="Gatsby can't find me"
            {...props}
          />
        )}
      </InlineImage>
      <h1>
        <InlineTextareaField name="rawFrontmatter.title" />
      </h1>
    </InlineForm>
  )
}
```

<!-- TODO: no way to edit alt text? implement and make example-->
