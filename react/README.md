# MANUAL React Native Style Guide

*A mostly reasonable approach to React and JSX*

This style guide is mostly based on the standards that are currently prevalent in JavaScript, although some conventions (i.e async/await or static class fields) may still be included or prohibited on a case-by-case basis. Currently, anything prior to stage 3 is not included nor recommended in this guide.

## Table of Contents

  1. [Basic Rules](#basic-rules)
  1. [Class vs `React.createClass` vs stateless](#class-vs-reactcreateclass-vs-stateless)
  1. [Mixins](#mixins)
  1. [Naming](#naming)
  1. [Declaration](#declaration)
  1. [Alignment](#alignment)
  1. [Quotes](#quotes)
  1. [Spacing](#spacing)
  1. [Props](#props)
  1. [Refs](#refs)
  1. [Parentheses](#parentheses)
  1. [Tags](#tags)
  1. [Methods](#methods)
  1. [Ordering](#ordering)
  1. [`isMounted`](#ismounted)

## Basic Rules

  <!-- - Only include one React component per file.
    - However, multiple [Stateless, or Pure, Components](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions) are allowed per file. eslint: [`react/no-multi-comp`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless).
  - Always use JSX syntax.
  - Do not use `React.createElement` unless you’re initializing the app from a file that is not JSX.
  - [`react/forbid-prop-types`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/forbid-prop-types.md) will allow `arrays` and `objects` only if it is explicitly noted what `array` and `object` contains, using `arrayOf`, `objectOf`, or `shape`. -->

- Only include one React component per file.

  ```tsx
  // bad
  // Multiple components in one file
  import React from 'react';
  import { View, Text } from 'react-native';

  const Header = () => (
    <View>
      <Text>Header</Text>
    </View>
  );

  const Footer = () => (
    <View>
      <Text>Footer</Text>
    </View>
  );

  // good
  // Header.tsx
  import React from 'react';
  import { View, Text } from 'react-native';

  export const Header = () => (
    <View>
      <Text>Header</Text>
    </View>
  );
  ```

- Always use JSX syntax.

  ```tsx
  // bad
  // Using React.createElement
  import React from 'react';
  import { View, Text } from 'react-native';

  const WelcomeMessage = () => React.createElement(View, null, React.createElement(Text, null, 'Welcome'));

  // good
  // WelcomeMessage.tsx
  import React from 'react';
  import { View, Text } from 'react-native';

  export const WelcomeMessage = () => (
    <View>
      <Text>Welcome</Text>
    </View>
  );
  ```

- Do not use `React.createElement` unless initializing the app from a non-JSX file.
  ```tsx
  // bad
  // Unnecessarily using React.createElement in a .tsx file
  const App = () => React.createElement('View', null, 'Hello World');

  // good
  // Directly using JSX syntax
  const App = () => <View>Hello World</View>;
  ```

- For prop types, use TypeScript's type annotations instead of `React.PropTypes`.
  ```tsx
  // bad
  // Using PropTypes in a TypeScript environment
  import PropTypes from 'prop-types';
  import React from 'react';
  import { Text, View } from 'react-native';

  const Greeting = ({ name }) => (
    <View>
      <Text>Hello, {name}</Text>
    </View>
  );

  Greeting.propTypes = {
    name: PropTypes.string.isRequired,
  };

  // good
  // Using TypeScript interfaces for component props
  import React from 'react';
  import { Text, View } from 'react-native';

  interface GreetingProps {
    name: string;
  }

  const Greeting: React.FC<GreetingProps> = ({ name }) => (
    <View>
      <Text>Hello, {name}</Text>
    </View>
  );
  ```

<!-- 
## Class vs `React.createClass` vs stateless

  - If you have internal state and/or refs, prefer `class extends React.Component` over `React.createClass`. eslint: [`react/prefer-es6-class`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md) [`react/prefer-stateless-function`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)

    ```jsx
    // bad
    const Listing = React.createClass({
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    });

    // good
    class Listing extends React.Component {
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    }
    ```

    And if you don’t have state or refs, prefer normal functions (not arrow functions) over classes:

    ```jsx
    // bad
    class Listing extends React.Component {
      render() {
        return <div>{this.props.hello}</div>;
      }
    }

    // bad (relying on function name inference is discouraged)
    const Listing = ({ hello }) => (
      <div>{hello}</div>
    );

    // good
    function Listing({ hello }) {
      return <div>{hello}</div>;
    }
    ``` -->
## Class Components vs. Functional Components
When deciding between using a class component or a functional component in React Native with TypeScript, consider the following:

If you have internal state, effects, or refs, prefer functional components with hooks over class components.

TypeScript provides excellent support for functional components, allowing for strong typing of props and state, and hooks offer a more concise and readable way to manage state, side effects, and more.

```tsx
// bad
import React, { Component } from 'react';
import { Text, View } from 'react-native';

interface State {
  hello: string;
}

interface Props {}

class Listing extends Component<Props, State> {
  state: State = {
    hello: 'World',
  };

  render() {
    return <View><Text>{this.state.hello}</Text></View>;
  }
}

// good
import React, { useState } from 'react';
import { Text, View } from 'react-native';

interface Props {
  initialHello: string;
}

const Listing: React.FC<Props> = ({ initialHello }) => {
  const [hello, setHello] = useState(initialHello);

  return <View><Text>{hello}</Text></View>;
};
```

For components without state or refs, use named function expressions.

This approach benefits from both readability and the ability to leverage TypeScript for prop type validation without the overhead of a class.

```tsx
// bad
import React, { Component } from 'react';
import { Text, View } from 'react-native';

class Listing extends Component<{ hello: string }> {
  render() {
    return <View><Text>{this.props.hello}</Text></View>;
  }
}

// bad (relying on function name inference is discouraged)
const Listing = ({ hello }: { hello: string }) => (
  <View><Text>{hello}</Text></View>
);

// good
import React from 'react';
import { Text, View } from 'react-native';

interface Props {
  hello: string;
}

const Listing: React.FC<Props> = ({ hello }) => {
  return <View><Text>{hello}</Text></View>;
};
```

## Mixins (Historical Context)

**Do not use mixins** in React Native projects. 

> Why? Mixins were an early pattern for reusing code between components, but they have been replaced by more explicit and modular solutions like hooks for functional components and higher-order components (HOCs). For more details, see React's legacy blog post ["Mixins Considered Harmful"](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html).


## Naming

  - **Extensions**: Use `.tsx` extension for React components that use JSX and .ts for files that do not contain JSX. This change is essential in a TypeScript project for proper syntax highlighting and tooling support.
  - **Filename**: Use PascalCase for filenames of components. This convention applies to both `.tsx` and `.ts` files when they define or contain a React component or a significant class.
  - **Reference Naming**: Use **PascalCase for React component imports** and **camelCase for their instances** within the code. This distinction helps clarify the difference between component classes and their instances, aligning with standard JavaScript object-oriented practices.

    ```tsx
    // bad
    import reservationCard from './ReservationCard';

    // good
    import ReservationCard from './ReservationCard';

    // bad
    const ReservationItem = <ReservationCard />;

    // good
    const reservationItem = <ReservationCard />;
    ```
    > Why? Using PascalCase for component names distinguishes them as constructors or classes, which are instantiable, whereas camelCase instances indicate that they are instances, adhering to typical JavaScript naming conventions.

  - **Component Naming**: Use the filename as the component name. For example, `ReservationCard.tsx` should have a reference name of `ReservationCard`. 

    ```tsx
    // bad
    import Footer from './Footer/Footer';

    // bad
    import Footer from './Footer/index';

    // good
    import Footer from './Footer';
    ```
    - For components defined in an `index.tsx` file within a directory, the component should still be given an explicit reference name, as you would for any other file, rather than defaulting to the directory name. This approach ensures consistency and clarity when importing and using these components elsewhere in the application.
    
      ```tsx
      // In Footer/index.tsx
      // Explicit component naming inside index.tsx
      export const Footer: React.FC = () => {
        return <View><Text>Footer Content</Text></View>;
      };

      // When importing
      import { Footer } from './Footer';  // Imports Footer from Footer/index.tsx
      ```

  - **Higher-order Component Naming**: Use a composite of the higher-order component’s name and the passed-in component’s name as the `displayName` on the generated component. For example, the higher-order component `withFoo()`, when passed a component `Bar` should produce a component with a `displayName` of `withFoo(Bar)`.

    > Why? A component’s `displayName` may be used by developer tools or in error messages, and having a value that clearly expresses this relationship helps people understand what is happening.

    ```tsx
    // bad
    export default function withFoo(WrappedComponent) {
      return function WithFoo(props) {
        return <WrappedComponent {...props} foo />;
      }
    }

    // good
    export default function withFoo(WrappedComponent) {
      function WithFoo(props) {
        return <WrappedComponent {...props} foo />;
      }

      const wrappedComponentName = WrappedComponent.displayName
        || WrappedComponent.name
        || 'Component';

      WithFoo.displayName = `withFoo(${wrappedComponentName})`;
      return WithFoo;
    }
    ```

  - **Props Naming**
    - **Respect the standard usage of React Native prop names**: Avoid repurposing well-established prop names in React Native for different meanings. For instance, use the `style` prop as intended for passing styling properties to elements.

      ```tsx
      // bad
      <MyComponent style="coral" />  // Misleading use of 'style' as a string.

      // good
      <MyComponent style={{ backgroundColor: 'coral' }} />  // Correct use of 'style' with an object.
      <MyComponent variant="coral" />  // Alternative prop for variant styles.
      ```
    - **Use semantically clear prop names**: It's generally acceptable to use props like `id`, `name`, `options`, etc., but ensure the name is clear and consistent with the context of the element and the data contained within the prop. Prop names should intuitively convey their function without ambiguity.

## Declaration

  - Do not use `displayName` for naming components.
    > Why? Explicitly naming your components by their reference, rather than relying on the `displayName` property, promotes clearer, more maintainable code. This practice is particularly important in TypeScript projects where type inference and editor integrations rely heavily on explicit names.

    ```tsx
    // bad
    const ReservationCard = React.createClass({
      displayName: 'ReservationCard',
      // stuff goes here
    });

    // good (explicitly naming the component)
    export default class ReservationCard extends React.Component {
      // stuff goes here
    }

    // better (modern approach with functional components)
    export const ReservationCard: React.FC = () => {
      // stuff goes here
    };
    ```

  - Use Functional Components Over Class Components.
    > Given the shift towards functional components in the React ecosystem, especially with the widespread adoption of hooks, it's beneficial to prefer functional components over class components unless specific class features are needed (e.g., error boundaries).
    ```tsx
    // good (class component)
    export default class ReservationCard extends React.Component {
      // component logic
    }

    // better (functional component)
    export const ReservationCard: React.FC = () => {
      // component logic
    };
    ```
  - Functional Component Declaration
    - Preferred Method: Arrow Functions Assigned to Constants
      > Why? Using arrow functions helps maintain a consistent style across your codebase, and is the most common way in which code examples are defined in both React Native and Expo documentation.

      ```tsx
      // Recommended
      export const ReservationCard: React.FC = () => {
        return <View><Text>Reservation Details</Text></View>;
      };
      ```
      > Less Error-Prone: By capturing the `this` of the enclosing context, arrow functions reduce common errors associated with JavaScript’s dynamic `this`. Generally suitable for most React Native components unless hoisting is specifically required.

    - Alternative Method: Named Function Declarations
      ```tsx
      // Alternative when hoisting is needed
      function ReservationCard() {
        return <View><Text>Reservation Details</Text></View>;
      }
      ```
      > Why? Named function declarations are beneficial when component definitions need to be referenced before their declaration point due to their hoisting nature.

## Alignment

  - Follow these alignment styles for JSX syntax. eslint: [`react/jsx-closing-bracket-location`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md) [`react/jsx-closing-tag-location`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-closing-tag-location.md)

    ```tsx
    // bad
    <Foo superLongParam="bar"
         anotherSuperLongParam="baz" />

    // good
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    />

    // if props fit in one line then keep it on the same line
    <Foo bar="bar" />

    // children get indented normally
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    >
      <Quux />
    </Foo>

    // bad
    {showButton &&
      <Button />
    }

    // bad
    {
      showButton &&
        <Button />
    }

    // good
    {showButton && (
      <Button />
    )}

    // good
    {showButton && <Button />}

    // good
    {someReallyLongConditional
      && anotherLongConditional
      && (
        <Foo
          superLongParam="bar"
          anotherSuperLongParam="baz"
        />
      )
    }

    // good
    {someConditional ? (
      <Foo />
    ) : (
      <Foo
        superLongParam="bar"
        anotherSuperLongParam="baz"
      />
    )}
    ```

## Quotes

  - Always use double quotes (`"`) for JSX attributes, but single quotes (`'`) for all other JS. eslint: [`jsx-quotes`](https://eslint.org/docs/rules/jsx-quotes)

    > Why? Regular HTML attributes also typically use double quotes instead of single, so JSX attributes mirror this convention.

    ```tsx
    // bad
    <Foo bar='bar' />

    // good
    <Foo bar="bar" />

    // bad
    <Foo style={{ left: "20px" }} />

    // good
    <Foo style={{ left: '20px' }} />
    ```

## Spacing

  - Always include a single space in your self-closing tag. eslint: [`no-multi-spaces`](https://eslint.org/docs/rules/no-multi-spaces), [`react/jsx-tag-spacing`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-tag-spacing.md)

    ```tsx
    // bad
    <Foo/>

    // very bad
    <Foo                 />

    // bad
    <Foo
     />

    // good
    <Foo />
    ```

  - Do not pad JSX curly braces with spaces. eslint: [`react/jsx-curly-spacing`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)

    ```tsx
    // bad
    <Foo bar={ baz } />

    // good
    <Foo bar={baz} />
    ```

## Props

  - Always use camelCase for prop names, or PascalCase if the prop value is a React component.

    ```tsx
    // bad
    <Foo
      UserName="hello"
      phone_number={12345678}
    />

    // good
    <Foo
      userName="hello"
      phoneNumber={12345678}
      Component={SomeComponent}
    />
    ```

  - Omit the value of the prop when it is explicitly `true`. eslint: [`react/jsx-boolean-value`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

    ```tsx
    // bad
    <Foo
      hidden={true}
    />

    // good
    <Foo
      hidden
    />

    // good
    <Foo hidden />
    ```

  - Always include an `accessibilityLabel` prop on `<Image>` components if the image conveys meaningful information. If the image is purely decorative, you can set `accessible={false}` to indicate that the image should be ignored by screen readers. This aligns with accessibility standards in React Native, helping all users get the most out of your app.

    ```tsx
    // bad
    <Image source={require('hello.jpg')} />

    // good (image conveys meaningful information)
    <Image source={require('hello.jpg')} accessibilityLabel="Me waving hello" />

    // good (decorative image)
    <Image source={require('hello.jpg')} accessible={false} />

    ```

  - Do not use phrases like "image", "photo", or "picture" in `accessibilityLabel` props for `<Image>` components. This follows the same rationale: such descriptors are typically redundant because the context in which the `<Image>` is used should make it clear it is graphical content, or the content itself should be described effectively without stating it is an image.

    > Why? While screen readers may not automatically describe elements like they might on the web. When a visually impaired person uses a screen reader and navigates to an image, the `accessibilityLabel` serves as the descriptive text for that image. Using terms like "image" or "photo" within this label is usually unnecessary.

    ```tsx
    // bad
    <Image source={require('hello.jpg')} accessibilityLabel="Picture of me waving hello" />

    // good
    <Image source={require('hello.jpg')} accessibilityLabel="Me waving hello" />
    ```

  - Use only appropriate `accessibilityRole` values: Ensure that you use only the roles supported by React Native, which describe the element's function in a way that makes sense when no visual cues are available.

    ```tsx
    // bad - not a supported role in React Native
    <View accessibilityRole="datepicker" />

    // bad - using a vague role
    <View accessibilityRole="range" />

    // good
    <TouchableOpacity accessibilityRole="button" />
    ```

  - Avoid using an array index as a `key` prop; prefer a stable ID. 
    > Why? Not using a stable ID [is an anti-pattern](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318) because it can negatively impact performance and cause issues with component state.

    We don’t recommend using indexes for keys if the order of items may change.

    ```tsx
    // bad
    {todos.map((todo, index) =>
      <Todo
        {...todo}
        key={index}
      />
    )}

    // good
    {todos.map(todo => (
      <Todo
        {...todo}
        key={todo.id}
      />
    ))}
    ```
    - Exception: If you are rendering a static list where the order does not change and there are no dynamic updates, using an index as a key may be acceptable. This is particularly true for simple lists of static content where no state or props of the list items will change in response to user interactions.

      ```tsx
      // Acceptable when the list is static and does not change
      {staticItems.map((item, index) => (
        <StaticItemComponent {item} key={index} />
      ))}
      ```


  - Prefer TypeScript type definitions over propTypes

    > Why? TypeScript provides compile-time type checking which enhances code reliability and developer productivity by catching errors early. Unlike propTypes, TypeScript types are integrated into the language, offering a more robust and versatile way to define component interfaces.

    ```tsx
    // bad (using propTypes)
    function SFC({ foo, bar }) {
      return <div>{foo}{bar}</div>;
    }
    SFC.propTypes = {
      foo: PropTypes.number.isRequired,
      bar: PropTypes.string,
    };

    // good (using TypeScript)
    interface SFCProps {
      foo: number; // Required
      bar?: string;
    }

    const SFC: React.FC<SFCProps> = ({ foo, bar = '' }) => {
      return <div>{foo}{bar}</div>;
    };
    ```

  - Always assign default values to optional props directly in the component signature
    > Why? This practice ensures that all components have predictable behaviour and reduces the need for additional type checks within the component body. It also helps document the component’s expected behaviour without external references.

    ```tsx
    interface SFCProps {
      foo: number;
      bar?: string; // Optional with default
    }
    
    // Empty string set as default value for `bar`
    const SFC: React.FC<SFCProps> = ({
      foo,
      bar = '', 
    }) => {
      return <div>{foo}{bar}</div>;
    };
    ```

## Refs
Preferred Method: `useRef` Hook
  - Use `useRef` for functional components. This is the modern and recommended approach for managing refs in React. `useRef` provides a more consistent and safer way to access React elements.

    > Why? `useRef` returns a mutable ref object whose .current property is initialized with the passed argument (default is null). This object persists for the full lifetime of the component, making it ideal for tracking a a component without re-rendering issues.

    ```tsx
    import React, { useRef } from 'react';

    function FooComponent() {
      const myRef = useRef(null);

      return <Foo ref={myRef} />;
    }
    ```

Alternative Method: Callback Refs
  - Use callback refs when more control is needed over the ref's lifecycle or in class components. Callback refs provide the flexibility to set and clean up refs directly in component lifecycle methods.

    > Why? Callback refs give you direct access to the DOM element or React node, allowing for more complex interactions such as integrating with third-party DOM libraries. They are particularly useful in class components or situations where the ref needs to be attached or detached at specific times.
    ```tsx
    class FooComponent extends React.Component {
      myRef = null;

      componentDidMount() {
        // You can now access this.myRef here if needed
      }

      render() {
        return <Foo ref={(ref) => { this.myRef = ref; }} />;
      }
    }
    ```

## Parentheses

  - Wrap JSX tags in parentheses when they span more than one line. eslint: [`react/jsx-wrap-multilines`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-wrap-multilines.md)

    ```tsx
  // bad
  const MyComponent = () => {
    return <MyContainer variant="long body" foo="bar">
            <MyChild />
          </MyContainer>;
  }

  // good
  const MyComponent = () => {
    return (
      <MyContainer variant="long body" foo="bar">
        <MyChild />
      </MyContainer>
    );
  }

  // good, when single line
  const MyComponent = () => {
    const body = <div>hello</div>;
    return <MyContainer>{body}</MyContainer>;
  }
    ```

## Tags

  - Always self-close tags that have no children. eslint: [`react/self-closing-comp`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)

    ```tsx
    // bad
    <Foo variant="stuff"></Foo>

    // good
    <Foo variant="stuff" />
    ```

  - If your component has multiline properties, close its tag on a new line. eslint: [`react/jsx-closing-bracket-location`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

    ```tsx
    // bad
    <Foo
      bar="bar"
      baz="baz" />

    // good
    <Foo
      bar="bar"
      baz="baz"
    />
    ```

## Methods

  - Use arrow functions to close over local variables. It is handy when you need to pass additional data to an event handler. Although, make sure they [do not massively hurt performance](https://www.bignerdranch.com/blog/choosing-the-best-approach-for-react-event-handlers/), in particular when passed to custom components that might be PureComponents, because they will trigger a possibly needless rerender every time.

    ```jsx
    function ItemList(props) {
      return (
        <ul>
          {props.items.map((item, index) => (
            <Item
              key={item.key}
              onClick={(event) => { doSomethingWith(event, item.name, index); }}
            />
          ))}
        </ul>
      );
    }
    ```

  - Bind event handlers for the render method in the constructor. eslint: [`react/jsx-no-bind`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

    > Why? A bind call in the render path creates a brand new function on every single render. Do not use arrow functions in class fields, because it makes them [challenging to test and debug, and can negatively impact performance](https://medium.com/@charpeni/arrow-functions-in-class-properties-might-not-be-as-great-as-we-think-3b3551c440b1), and because conceptually, class fields are for data, not logic.

    ```jsx
    // bad
    class extends React.Component {
      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv.bind(this)} />;
      }
    }

    // very bad
    class extends React.Component {
      onClickDiv = () => {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv} />
      }
    }

    // good
    class extends React.Component {
      constructor(props) {
        super(props);

        this.onClickDiv = this.onClickDiv.bind(this);
      }

      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv} />;
      }
    }
    ```

  - Do not use underscore prefix for internal methods of a React component.
    > Why? Underscore prefixes are sometimes used as a convention in other languages to denote privacy. But, unlike those languages, there is no native support for privacy in JavaScript, everything is public. Regardless of your intentions, adding underscore prefixes to your properties does not actually make them private, and any property (underscore-prefixed or not) should be treated as being public. See issues [#1024](https://github.com/airbnb/javascript/issues/1024), and [#490](https://github.com/airbnb/javascript/issues/490) for a more in-depth discussion.

    ```jsx
    // bad
    React.createClass({
      _onClickSubmit() {
        // do stuff
      },

      // other stuff
    });

    // good
    class extends React.Component {
      onClickSubmit() {
        // do stuff
      }

      // other stuff
    }
    ```

  - Be sure to return a value in your `render` methods. eslint: [`react/require-render-return`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/require-render-return.md)

    ```jsx
    // bad
    render() {
      (<div />);
    }

    // good
    render() {
      return (<div />);
    }
    ```

## Ordering

  - Ordering for `class extends React.Component`:

  1. optional `static` methods
  1. `constructor`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *event handlers starting with 'handle'* like `handleSubmit()` or `handleChangeDescription()`
  1. *event handlers starting with 'on'* like `onClickSubmit()` or `onChangeDescription()`
  1. *getter methods for `render`* like `getSelectReason()` or `getFooterContent()`
  1. *optional render methods* like `renderNavigation()` or `renderProfilePicture()`
  1. `render`

  - How to define `propTypes`, `defaultProps`, `contextTypes`, etc...

    ```jsx
    import React from 'react';
    import PropTypes from 'prop-types';

    const propTypes = {
      id: PropTypes.number.isRequired,
      url: PropTypes.string.isRequired,
      text: PropTypes.string,
    };

    const defaultProps = {
      text: 'Hello World',
    };

    class Link extends React.Component {
      static methodsAreOk() {
        return true;
      }

      render() {
        return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>;
      }
    }

    Link.propTypes = propTypes;
    Link.defaultProps = defaultProps;

    export default Link;
    ```

  - Ordering for `React.createClass`: eslint: [`react/sort-comp`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

  1. `displayName`
  1. `propTypes`
  1. `contextTypes`
  1. `childContextTypes`
  1. `mixins`
  1. `statics`
  1. `defaultProps`
  1. `getDefaultProps`
  1. `getInitialState`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *clickHandlers or eventHandlers* like `onClickSubmit()` or `onChangeDescription()`
  1. *getter methods for `render`* like `getSelectReason()` or `getFooterContent()`
  1. *optional render methods* like `renderNavigation()` or `renderProfilePicture()`
  1. `render`

## `isMounted`

  - Do not use `isMounted`. eslint: [`react/no-is-mounted`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

  > Why? [`isMounted` is an anti-pattern][anti-pattern], is not available when using ES6 classes, and is on its way to being officially deprecated.

  [anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html

## Translation

  This JSX/React style guide is also available in other languages:

  - ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese (Simplified)**: [jhcccc/javascript](https://github.com/jhcccc/javascript/tree/master/react)
  - ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Chinese (Traditional)**: [jigsawye/javascript](https://github.com/jigsawye/javascript/tree/master/react)
  - ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Español**: [agrcrobles/javascript](https://github.com/agrcrobles/javascript/tree/master/react)
  - ![jp](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/javascript-style-guide](https://github.com/mitsuruog/javascript-style-guide/tree/master/react)
  - ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [apple77y/javascript](https://github.com/apple77y/javascript/tree/master/react)
  - ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [pietraszekl/javascript](https://github.com/pietraszekl/javascript/tree/master/react)
  - ![Br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Portuguese**: [ronal2do/javascript](https://github.com/ronal2do/airbnb-react-styleguide)
  - ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**: [leonidlebedev/javascript-airbnb](https://github.com/leonidlebedev/javascript-airbnb/tree/master/react)
  - ![th](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Thailand.png) **Thai**: [lvarayut/javascript-style-guide](https://github.com/lvarayut/javascript-style-guide/tree/master/react)
  - ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [alioguzhan/react-style-guide](https://github.com/alioguzhan/react-style-guide)
  - ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [ivanzusko/javascript](https://github.com/ivanzusko/javascript/tree/master/react)
  - ![vn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnam**: [uetcodecamp/jsx-style-guide](https://github.com/UETCodeCamp/jsx-style-guide)

**[⬆ back to top](#table-of-contents)**
