# Markdown Object Markup Langauge

MOML is a spec for structuring a markdown file in order to easily extract a hierachical markup and turn it into an object, dictionary, or array. It was developed from the need to have simple and editor-friendly Markdown files and documents while retaining various metadata concerning the document.

At the time of development, there was no way to structure a Markdown document in which one can easily specify metadata either without the use of cumbersome tabs (like YAML) or by creating a custom format. And we all know what happens when we introduce more [standards](https://xkcd.com/927/). MOML is solving this problem by creating yet another custom standard which will hopefully unifying everything.

```
YAMLLookAlikes++;
```

## Examples

A great way to illustrate why one might need MOML is by looking at a common usecase scenario is by looking at blog posts. Lots of people today write blogposts solely in markdown and often into actual files (as opposed to using an online editor). This means that the file has to somehow be able to convey some basic information:

1. post title
2. post body
3. publication date
4. tags
5. and possibly short description

Jekyll, a popular blogging engine, proposes to solve this by using front matter in combination with YAML like so:

```markdown
---
title: My post title
date: 2016-1-11
tags: first post, tech, webdev
short_description: My short description starts here
---

Post body goes here
```

The issue here is that traditional YAML is horrendous for any long string input, one that might rival the size of the body. In MOML, you'd rewrite the above as:

```markdown
title: My post title
---
date: 2016-1-11
---
tags: first post, tech, webdev
---
short_description:

My short description
---
content:

My post body
```

When the short description expands, YAML doesn't handle that well. The string has to wrap around or use special syntax which requires text indenting. With MOML, you retain the flat structure. 

Another great use case for MOML is adding images to a slideshow on a post. In YAML, you can use it as such:

```yaml
pictures:
  - my-pic.jpg
  - your-pic.jpg
```

And so on, again, requiring indenting. Imagine if you wanted to include entire HTML snippets, the array notation quickly breaks down:

```yaml
sections:
  - <h1>My section</h1>
    <p>This is my section</p>
  - <h1>Your section</h1>
    <p>Your section</p>
```

In MOML, it'd look a little more inviting:

```markdown
sections[]:

<h1>My section</h1>
<p>This is my section</p>
---
sections[]:

<h1>Your section</h1>
<p>Your section</p>
```

## Usage

MOML is pretty simple and straightforward. Checkout the [implementation](https://github.com/AntJanus/popstar-file-reader/blob/master/src/parser.es6).

MOML at its heart wants to allow your metadata to live alongside your document's contents and be treated with the same priority as the content itself, having to endure to limitatition or tabbing in order to achieve some kind of structure. MOML also strives to avoid XML and the likes as they still require indenting for legibility. MOML is Markdown-first and friendly.

### Simple variables

To create a simple variable, simply declare its name followed by a colon like so:

```
myVar: 
```

It *can* be left blank but what's the fun in that? Let's add a value to it:

```
myVar: This is my variable value
```

You may prepend your content with several line breaks in order to achieve a nicer styling for longer content like so:

```
myContent:

My content goes here
```

Caveat: Redeclaring a variable will result in it being overwritten unless it's an array.

### Multiple variables

Multiple variable declarations are separated by three dashes or more (depending on your style) like so:

```markdown
myVar: This is my variable
---
yourVar: This is your variable
```

The following is also legal:

```markdown
myVar: This is my variable
---------------------------------------------------
yourVar: This is your variable
```

### Array notation

The ability to store repeated values such as image paths for a slideshow. To differentiate a simple variable from an array, add array brackets to the variable name. Each redeclaration of that array name will add another element into the resulting array like so:

```markdown
myVar: this is a plain variable
---
myArray[]: This is an array
---
myArray[]: This is the second element (or myArray[1]) in the array
---
myArray[]: Yet another
```

## Output

The [current implementation of the spec](https://github.com/AntJanus/popstar-file-reader/blob/master/src/parser.es6) will generate a Javascript object from MOML.

Here's an example:

```markdown
title: Test
---
tags[]: my tag
---
tags[]: tech
---
content:

My content
```

Gets translated into:

```javascript
{
  title: "Test",
  tags: [
    "my tag",
    "tech"
  ]
  content: "My content"
}
```

## To Do

- [ ] support for typing and/or type parsing like so: `string myType: value` or (using custom compilers) `markdown myContent: content` as well as `script myScript: script`
- [ ] short-form arrays using dash delimiters like so:
```
myArray[]:

- first element
- second element
```
