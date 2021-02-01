# IDOM - It's React, but in Python

[IDOM](https://github.com/idom-team/idom) is a new declarative Python package for
building highly interactive and composable user interfaces.

![idom logo](https://github.com/idom-team/idom/raw/929d07ff4a643320a6148336613621242284f8d2/docs/source/branding/idom-logo.png)

IDOM takes obvious inspiration from [React](https://reactjs.org/), and wherever
possible, attempts to achieve parity with the features it copies more directly. Nowhere
is this more evident than the version of React's often lauded
["Hooks"](https://reactjs.org/docs/hooks-intro.html) that IDOM implements in Python.

At a glance, the similarities between IDOM and React are rather striking. Below is a
React component which defines a simple `Slideshow` displaying an image that updates when
a user clicks on it, and immediately below it is the same view implemented in Python
using IDOM:

```JSX
import React, { useState } from react;

function Slideshow() {
  const [index, setIndex] = useState(0);
  return (
    <img
      src={ `https://picsum.photos/800/300?image=${index}` }
      onClick={ () => setIndex(index + 1) }
      style={ {cursor: "pointer"} }
    />
  )
}
```

```python
import idom

@idom.component
def Slideshow():
    index, set_index = idom.hooks.use_state(0)
    return idom.html.img(
        {
            "src": f"https://picsum.photos/800/300?image={index}",
            "onClick": lambda event: set_index(index + 1),
            "style": {"cursor": "pointer"},
        }
    )

idom.run(Slideshow)
```

## Why Do We Need IDOM?

Over the [past 5 years](https://www.npmtrends.com/react-vs-angular-vs-vue) front-end
developers seem to have arrived at the conclusion that declarative programming is
usually better than imperative. Put more simply, mutable state in programs can quickly
lead to unsustainable complexity. This trend is largely evidenced by
[rise](https://gist.github.com/tkrotoff/b1caa4c3a185629299ec234d2314e190) of Javascript
frameworks like [Vue](https://vuejs.org/) and [React](https://reactjs.org/) which
describe the logic of computations without explicitly stating their control flow.

![npm download trends](https://gist.githubusercontent.com/rmorshea/24d369fac53c2e1a07557850a0e7ff13/raw/ac4f80fe824d8159b87545ff3632d7da9cf351ce/npm-download-trends.png)

So what does this have to do with Python and IDOM? Well, because browsers are the de
facto "operating system of the internet", even back-end languages like Python have had
to figure out clever ways to integrate with them. While standard
[REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs are well
suited to applications built using HTML templates, modern browser users expect a higher
degree of interactivity than this alone can achieve.

A variety of Python packages have since been created to help solve this problem:

- [IPyWidgets](https://github.com/jupyter-widgets/ipywidgets) - Adds interactive widgets
  to [Jupyter Notebooks](https://jupyter.org/)
- [Streamlit](https://www.streamlit.io/) - Turns simple Python scripts into interactive
  dashboards
- [Plotly Dash](https://plotly.com/dash/) - Allows data scientists to produces
  enterprise-ready analytic apps
- [Bokeh](https://docs.bokeh.org/) - An interactive visualization library for modern web
  browsers

However they each have drawbacks that can make them difficult to use.

1. **Imperative paradigm** - projects like IPyWidgets and Bokeh have not embraced the
   same declarative design principle pioneered by front-end developers.

2. **Limited capabilities** - At their initial inception, the development of these
   libraries was driven by the visualization needs of data scientists, so creating even
   moderately complex UI layouts is challenging.

3. **Restrictive ecosystems** - UI components developed for one framework cannot be
   easily ported to any of the others because their APIs are either too complex,
   undocumented, or are structurally inaccesible.

A future article will address specific comparisons to each of the projects mentioned
above, but for now we'll just focus on IDOM and its solutions to the problems above.

## Declarative Components

IDOM, by adopting the hook design pattern, inherits many of React's aesthetic and
functional characteristics. For those unfamiliar with hooks, user interfaces are
composed of basic [HTML elements](https://en.wikipedia.org/wiki/HTML_element) which are
constructed and returned by special functions called "components". Then, through the
magic of hooks, those functions can be made to have state. Consider the component below
which returns an HTML button element and tracks the number of times it has been clicked.

```python
import idom

@idom.component
def Counter():
    """A functional component for a button that tracks its click count"""
    count, set_count = idom.hooks.use_state(initial_value=0)
    return idom.html.div(
      idom.html.button({"onClick": lambda event: set_count(lambda old_count: old_count + 1)}),
      idom.html.p(f"Clicked {count} times"),
    )
```

The first time a view of the component above is rendered the `Counter` function
"declares" what should be displayed (the `button`) and the callbacks that should be
registered (the `onClick` handler) by returning a series of HTML elements. Machinery
behind the scenes then realizes that declaration. When a user clicks the now visible
button its callback is triggered, the component is re-rendered and the machinery again
goes to work to achieve that end. This time though, the `count` will have been
incremented when the HTML elements are constructed.

This behavior of defining outcomes without stating the means by which to achieve them is
what makes components in IDOM and React "declarative". For comparison, a hypothetical,
and a more imperative approach to defining the same interface might look similar to the
following:

```python
layout = Layout()

state = {"count": 0}
count_text = html.p(f"Clicked {state['count']} times")

def on_button_click(event):
    state['count'] += 1
    count_text.update(value=f"Clicked {state['count']} times")

count_button = html.button(on_click=on_button_click)
layout.add_element(html.div(count_button, count_text))
```

In this imperative incarnation, we must explicitely state how the `count_button` updates
`count_text` via its `on_click` callback. We should also note that `state` is mutated by
ammending its `'count'`. While neither declarative nor imperative design principle are
inherently better in all circumstances, it is often the case that asserting the way a
view should look is easier than describing how it should come to look that way.

## Composable Layouts

Perhaps more significant than the aesthetic

## Ecosystem Independence

## Conclusion

Building highly interactive web applications as a Python developer has historically been
a great challenge. However, IDOM changes that. Knowing just basic HTML, CSS, and Python,
you can make everything from
[slideshows](https://idom-docs.herokuapp.com/docs/examples.html#slideshow) to
[interactive
dashboards](https://idom-docs.herokuapp.com/docs/examples.html#simple-dashboard). Its
usage can be as broad or targeted as you need it to be, and
you can use it wherever you need it, whether that's in a [Jupyter
notebook](https://github.com/idom-team/idom-jupyter) or standard web application.
