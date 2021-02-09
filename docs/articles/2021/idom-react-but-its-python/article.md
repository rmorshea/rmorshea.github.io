# IDOM - It's React, but in Python

[IDOM](https://github.com/idom-team/idom) is a new declarative Python package for
building highly interactive and composable user interfaces.

![idom logo](https://github.com/idom-team/idom/raw/929d07ff4a643320a6148336613621242284f8d2/docs/source/branding/idom-logo.png)

IDOM takes inspiration from [React](https://reactjs.org/), and wherever possible,
attempts to achieve parity with the features it copies more directly. Nowhere is this
more evident than the version of React's often lauded
["Hooks"](https://reactjs.org/docs/hooks-intro.html) that IDOM implements in Python.

At a glance, the similarities between IDOM and React are rather striking. Below is a
React component which defines a simple `Slideshow` displaying an image that updates when
a user clicks on it:

```jsx
import React, { useState } from react;

function Slideshow() {
  const [index, setIndex] = useState(0);
  return (
    <img
      src={ `https://picsum.photos/400?image=${index}` }
      onClick={ () => setIndex(index + 1) }
      style={ {cursor: "pointer"} }
    />
  )
}
```

And this is the same component implemented in Python using IDOM:

```python
import idom

@idom.component
def Slideshow():
    index, set_index = idom.hooks.use_state(0)
    return idom.html.img(
        {
            "src": f"https://picsum.photos/400?image={index}",
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
lead to unsustainable complexity. This trend is largely evidenced by the
[rise](https://gist.github.com/tkrotoff/b1caa4c3a185629299ec234d2314e190) of Javascript
frameworks like [Vue](https://vuejs.org/) and [React](https://reactjs.org/) which
describe the logic of computations without explicitly stating their control flow.

![npm download trends](npm-download-trends.png)

So what does this have to do with Python and IDOM? Well, because browsers are the de
facto "operating system of the internet", even back-end languages like Python have had
to figure out clever ways to integrate with them. While standard
[REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs are well
suited to applications built using HTML templates, modern browser users expect a higher
degree of interactivity than this alone can achieve.

A variety of Python packages have since been created to help solve this problem:

- [IPyWidgets](https://github.com/jupyter-widgets/ipywidgets) - Adds interactive widgets
  to [Jupyter Notebooks](https://jupyter.org/)
- [Dash](https://plotly.com/dash/) - Allows data scientists to produces enterprise-ready
  analytic apps
- [Streamlit](https://www.streamlit.io/) - Turns simple Python scripts into interactive
  dashboards
- [Bokeh](https://docs.bokeh.org/) - An interactive visualization library for modern web
  browsers

However they each have drawbacks that can make them difficult to use.

3. **Restrictive ecosystems** - UI components developed for one framework cannot be
   easily ported to any of the others because their APIs are either too complex,
   undocumented, or are structurally inaccesible.

1. **Imperative paradigm** - IPyWidgets and Bokeh have not embraced the same declarative
   design principles pioneered by front-end developers. Streamlit and Dash on the
   otherhand, are decarative, but fall short of the features provided by React or Vue.

1. **Limited layouts** - At their initial inception, the developers of these libraries
   were driven by the visualization needs of data scientists so the ability to create
   complex UI layouts may not have been a primary engineering goal.

A future article will address specific comparisons to each of the projects mentioned
above, but for now, we'll just focus on IDOM and its solutions to the problems above.

## Ecosystem Independence

IDOM has a flexible set of core abstractions that allow it to easily interface with its
peers. At the time of writing, both Jupyter and Dash are supported, while Streamlit and
Bokeh are in the works:

- [idom-jupyter](https://github.com/idom-team/idom-jupyter) (try it now with
  [Binder](https://mybinder.org/v2/gh/idom-team/idom-jupyter/main?filepath=notebooks%2Fintroduction.ipynb))
- [idom-dash](https://github.com/idom-team/idom-dash)

By providing well defined interfaces and straighforward protocols, IDOM makes it easy to
swap out any part of the stack with an alternate implementation if you need to. For
example, if you need to use a different web server for your application, IDOM already
has 3 options to choose from or, use as blueprints to create your own:

- [Sanic](https://github.com/sanic-org/sanic)
- [Flask](https://github.com/pallets/flask)
- [Tornado](https://github.com/tornadoweb/tornado)

You can even target your usage of IDOM in your production-grade applications with IDOM's
Javascript [React client library](https://github.com/idom-team/idom-client-react). Just
install it in your front-end app and connect a back-end websocket that's serving up IDOM
models. IDOM's own [documentation](https://idom-docs.herokuapp.com/docs/index.html) acts
as a prime example for this targeted usage - most of the page is static HTML, but
embedded in it are interactive examples that feature live views being served from a web
socket:

![live-examples-in-docs](live-examples-in-docs.gif){: .shadow}

## Declarative Components

IDOM, by adopting the hook design pattern from React, inherits many of its aesthetic and
functional characteristics. For those unfamiliar with hooks, user interfaces are
composed of basic [HTML elements](https://en.wikipedia.org/wiki/HTML_element) that are
constructed and returned by special functions called "components". Then, through the
magic of hooks, those components can be made to have state. Consider the component below
which displays a basic representation of an
[AND-gate](https://en.wikipedia.org/wiki/AND_gate):

```python
import idom

@idom.component
def AndGate():
    input_1, toggle_1 = use_toggle()
    input_2, toggle_2 = use_toggle()
    return idom.html.div(
        idom.html.input(
            {"type": "checkbox", "onClick": lambda event: toggle_1()}
        ),
        idom.html.input(
            {"type": "checkbox", "onClick": lambda event: toggle_2()}
        ),
        idom.html.pre(f"{input_1} AND {input_2} = {input_1 and input_2}"),
    )

def use_toggle():
    state, set_state = idom.hooks.use_state(False)

    def toggle_state():
        set_state(lambda old_state: not old_state)

    return state, toggle_state

idom.run(AndGate)
```

![and-gate-demo](and-gate.gif){: .shadow}

Here's a very high level summary of how it works... the first time a view of the
component above is rendered, the `AndGate` function is called where the initial `state`
for `input_1` and `input_2` is `False`. The function then returns a series of HTML
elements with callbacks that respond to client-side events. Machinery behind the scenes
subsequently realizes that declaration and displays two checkbox buttons with the text
`False AND False = False`. Later, when a user clicks the now visible checkbox buttons,
client-side events are triggered, the associated callbacks respond by inverting the old
state from `False` to `True`, and a re-render of the component is scheduled. When
re-rendering, the function is again called, this time though, where `input_1` and
`input_2` have been updated to reflect the new `state`, thus causing the displayed text
to change.

Consider the fact that nowhere in the example above does the code describe how to evolve
the frontend view when events occur. Instead, it declares that, given a particular
state, this is how the view should look. It's then IDOM's responsibility to figure out
how to bring that declaration into being. This behavior of defining outcomes without
stating the means by which to achieve them is what makes components in IDOM and React
"declarative". For comparison, a hypothetical, and a more imperative approach to
defining the same interface might look similar to the following:

```python
layout = Layout()

def and_gate():
    state = {"input_1": False, "input_2": False}
    output_text = html.pre()
    output_text.update(children=make_and_gate_text(state))

    def toggle_input(index):
      state[f"input_{index}"] = not state[f"input_{index}"]
      output_text.update(children=make_and_gate_text(state))

    return html.div(
        html.input(
            {"type": "checkbox", "onClick": lambda e: toggle_input(1)}
        ),
        html.input(
            {"type": "checkbox", "onClick": lambda e: toggle_input(2)}
        ),
        output_text
    )

def make_and_gate_text(state):
    return "{input_1} AND {input_2} = {output}".format(
        input_1=state["input_1"],
        input_2=state["input_2"],
        output=state["input_1"] and state["input_2"],
    )

layout.add_element(and_gate())
layout.run()
```

In this imperative incarnation there are several disadvantages:

1. **Program cannot easily be refactored** - Functions in the imperative example are
   much more specialized to their particular usages in `and_gate`. By comparison,
   `use_toggle` from the declarative implementation could be applicable to any scenario
   where boolean indicators should be toggled on and off.

2. **The view is not defined in static relations** - This issue is exemplified by the
   fact that we must call `make_and_gate_text` from two different locations in the
   program. Once in the body of `and_gate` and again in the body of the callback
   `toggle_input`. This means that, to understand what the `output_text` might contain,
   we must also understand all the business logic that surrounds it.

3. **Referential linkages cause complexity** - For the imperative implementation to
   evolve the view, its various callbacks must hold references to all the elements that
   they will update. At the outset this makes writing programs difficult since elements
   must be passed up and down the call stack wherever they are needed. Considered
   further though, it also means that a function layers down in the call stack can
   accidentally or intentionally impact the behavior of ostensibly unrelated parts of
   the program.

<!-- ## Flexible Layouts

Constructing complex layouts is also made easier when done declaratively because the
elements, state, and logic that comprise them are not entangled. As in the `OnOff`
component shown above, code responsible for managing business logic and manipulating
state can be clearly detached from the code responsible for structuring the elements of
the layout. The great advantage of this approach is that these sections of code can be
easily factored out into separate functions if either the logic or the structure becomes
too complex:

```python
@idom.component
def OnOff():
    return on_off_buttons(*use_on_off_state())

def use_on_off_state():
    """manage logic and state"""
    state, set_state = idom.hooks.use_state(False)

    def set_on():
        set_state(True)

    def set_off()
        set_state(False)

    return state, set_on, set_off

def on_off_buttons(state, set_on, set_off):
    """define element structure"""
    return idom.html.div(
        idom.html.button({"onClick": lambda event: set_on(), "On"),
        idom.html.button({"onClick": lambda event: set_off(), "Off"),
        idom.html.p("The button is " + ("on" if state else "off")),
    )
```

While the refactoring above is overkill in such a simple case, attempting something
similar with the ealier imperative example wouldn't be as straighforward because
callbacks responsible for defining business logic must hold a reference to the elements
they intend to update. The effect is that the description of the layout in code is often
muddled by semantic limitations of the business logic that make it difficult to maintain
as the code develops and grows old. -->

## Conclusion

Building highly interactive web applications as a Python developer has historically been
a great challenge. However IDOM changes that. Knowing just basic HTML, CSS, and Python,
you can make everything from
[slideshows](https://idom-docs.herokuapp.com/docs/examples.html#slideshow) to
[dashboards](https://idom-docs.herokuapp.com/docs/examples.html#simple-dashboard) and
use it wherever you need it, whether that's in a
[Jupyter Notebook](https://github.com/idom-team/idom-jupyter)
or an existing web application.

To learn more check it out:

- [installation instructions](https://idom-docs.herokuapp.com/docs/installation.html)
- [where to get started](https://idom-docs.herokuapp.com/docs/getting-started.html)
- [interactive examples](https://idom-docs.herokuapp.com/docs/examples.html)
- [and much more](https://idom-docs.herokuapp.com/docs/index.html)!
