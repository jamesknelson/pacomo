# A Method For Structuring Stylesheets in React-based Applications

*Read the [introduction](http://jamesknelson.com/a-system-for-structuring-your-react-applications-styles/).*

If you've ever wondered where to put styles, how to choose their selectors, or why one component's styles have somehow affected another's - Pacomo is for you.

By designing your app around the Pacomo method, you'll increase your productivity on multiple fronts:

- Spend less time choosing where to put things, and more time making them amazing
- Take advantage of tools built to ease development with Pacomo
- Ease interaction between teams following the same method

Pacomo accomplishes this by following three principles:

- Convention Over Configuration
- Separation Of Concerns
- Don't Repeat Yourself

*Pacomo was extracted from [memamug.com](http://www.memamug.com), a small [open-source](https://github.com/search?q=user%3Ajamesknelson+memamug) app by [James K Nelson](http://www.jamesknelson.com).*

## Status

**The Pacomo specification is currently at version 0.1, and should be considered unstable.**

## Rules

### Contents

1. [Naming conventions](#naming-conventions)
2. [Dependencies](#dependencies)
3. [Project structure](#project-structure)
4. [JavaScript guidelines](#javascript-guidelines)
5. [Stylesheet guidelines](#stylesheet-guidelines)

<a id="naming-conventions"></a>

### 1. Naming Conventions

#### Package Component Modifier

The core of PaCoMo is a simple process for deciding class names:

- Class names **must** start with your *Package* name

- The package name **must** be followed by the name of the *Component* in which they're defined

- The root element of each component **must** have a class with only the two parts defined above

- All other classes **must** end with a *Modifier* which describes the effect they have.

##### Example: Component root element class

Here is an example of the class used on the root `<div>` from the `memamug` package's `Paper` component:

```
memamug-Paper
```

##### Example: Modifiers

This example is for a class which adds rounded corners to the above `Paper` component:

```
memamug-Paper-rounded
```

#### Choosing names

Your *package name* **should** follow the name in your `package.json`.

Your *component name* **must** be identical to your component's JavaScript class name. This allows us to refer to it programatically using JSX's `displayName`, or `this.constructor.name` with ES6 classes.

<a id="dependencies"></a>

### 2. Dependencies

#### Normalize browser differences

To improve consistency between browsers, Pacomo-based applications (and applications which include Pacomo-based components) **must** include [normalize.css](https://necolas.github.io/normalize.css/).

#### Avoid dependencies with global styles

Pacomo-based components **must not** include any global styles outside of those following the PaCoMo naming convention.

By extension, they **must not** depend on any external stylesheets which include global styles; Bootstrap and Foundation are examples.

#### Build system

Following the *Don't Repeat Yourself* principle, Pacomo apps **must** use a CSS pre-processor which supports the `&-` notation; LESS and SASS are good examples.

In addition, your project **must** use a compiler which allows you to include stylesheets directly from your component JavaScript files; Webpack is one example.

<a id="project-structure"></a>

### 3. Project Structure

#### Components

Each React component **must** have a single stylesheet file, placed in the same directory as it's single JavaScript file.

The stylesheet **should** be required by the JavaScript file using `require()`, or similar.

#### Theme

In addition to component stylesheets, your project **may** have an overarching *theme* stylesheet. When compiled, this *theme* stylesheet **must not** produce any output, but instead be composed entirely of variables, mixins, etc.

If present, your *theme* stylesheet **may** be maintained as a separate package to your main application.

#### Application

Any styles on raw HTML (i.e. that not managed by React components) **must** be managed by the application, independent of any component styles.

<a id="javascript-guidelines"></a>

### 4. JavaScript Guidelines

#### Class name order

The top-level node returned by each component's `render` **should** include a `className` attribute with the following order:

1. root class
2. modifier classes
3. classes passed through `className` prop 

This ensures that classes passed as properties will be given priority over the component's default styles.

##### Example

Here is an example of the JSX used to define a top-level node in the `Paper` component found in the `memamug` package:

```
<div className={`memamug-Paper memamug-Paper-rounded ${this.props.className}` />
```

#### Don't repeat package and component strings

Following the *Don't Repeat Yourself* principle, package and component names **should** be written out as few times as possible. In practice, this means:

- Package name **should** be specified once, and added programatically thereafter
- Component name **should** be automatically prepended based on JSX's `displayName` property, or an ES6 class's `name` property.

<a id="stylesheet-guidelines"></a>

### 5. Stylesheet Guidelines

#### Reference themes where possible

If your compile-to-CSS language supports it, you **should** use `@import (reference) '...';` where possible to ensure no global styles are imported.  

#### One top-level selector

Following the *Separation Of Concerns* principle, each component stylesheet **must** have *exactly one* top-level selector. This selector **must** be named after the root class.

##### Example

For the `Paper` class in the `memamug` package, the component class would follow this pattern:

```
.memamug-Paper {
	/* ... */
}
```

#### Use `&-`

In order to make stylesheets simpler to reason about, all parts of all non-top-level selectors **must** start with `&-`; an operator available in compile-to-CSS languages which works as follows:

*Input:*

```
.memamug-Paper {
	box-shadow: 0 1px 4px rgba(0, 0, 0, 0.24);

	&-rounded {
		border-radius: 3px;
	}
}
```

*Output:*

```
.memamug-Paper {
	box-shadow: 0 1px 4px rgba(0, 0, 0, 0.24);
}

.memamug-Paper-rounded {
	border-radius: 3px;
}
```

This rule has a number of flow-on effects:

- You cannot select elements based on tag names or ids. Instead, select child elements through the use of a modifier class.
- You cannot apply styles to child components by referencing their element/root class names. Instead, pass in modifier classes to their `className` attribute, and add styles using these modifiers.

## Tools

These tools are not required by the specification, but may make implementation easier. If you have tools you'd like to add, feel free to make a pull request.

- [react-c](https://github.com/jamesknelson/react-c) - James K Nelson's original implementation

## Contributing

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

Pull requests welcome, but make them good.

## License

This document has been placed in the [Public Domain](http://creativecommons.org/publicdomain/zero/1.0/).
