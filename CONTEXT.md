# SmoothScroll

A desktop utility that turns discrete mouse-wheel notches into configurable eased motion at the OS input layer. This context covers the input pipeline, the engine that reschedules motion, and the surfaces the product ships on.

## Language

### Input and identity

**Wheel event**:
The unit of wheel input as it arrives from the OS hook, carrying a raw signed amount.
_Avoid_: mouse event, scroll event

**Semantic identity**:
The axis and modifier keys a wheel event arrived with, captured once at the event boundary so that synthetic output can reproduce them.
_Avoid_: metadata, event context

**Semantic pulse**:
The engine's unit of output: a signed amount of work together with the semantic identity it must be emitted under. One axis produces at most one pulse per frame.
_Avoid_: frame, delta, tick, motion, wheel event

**Notch**:
One physical wheel detent. The granularity at which hardware and most application controls agree on what "one scroll" means.
_Avoid_: step, click

### Granularity and compatibility

**Whole-notch output**:
The strategy that emits only exact multiples of a notch, one detent at a time, never sub-notch fractions.
_Avoid_: discrete mode, notch mode

**Discrete-wheel app**:
An application whose wheel receivers act only on whole notches (REAPER-class). Membership forces whole-notch output regardless of the user's selected mode.
_Avoid_: discrete app, notch app

**Discrete control**:
A native UI control under the cursor that consumes wheel input in whole-notch steps (spinbox, slider, combo box, list). Detection is per-message and geometric, and it outranks the discrete-wheel app rule.
_Avoid_: discrete widget, native control

**Pulse train**:
The stream of sub-notch pulses an engine mode emits between two notches. Controls that count notches mis-step or ignore this stream, which is the reason the compatibility concepts above exist.
_Avoid_: pulse sequence

### Timing and payout

**Window model**:
The engine's mechanism for handing a received scroll amount over across its own duration, where overlapping windows add and hand-over is exactly conservative.
_Avoid_: animation model, scroll model

**Window**:
A unit of the window model: a scroll amount with a remaining balance, an age, and a payout curve. This word is overloaded in this repo — it never means an operating-system window, and `EngineTiming::Windows` names this model rather than the OS.
_Avoid_: animation, OS window (for this sense)

**Payout**:
The pixels a window hands over in one frame. Measured in pixels; wheel units are derived afterwards.
_Avoid_: emission, output, delta

**Timing scheduler**:
The selector between the window model and the earlier running-total scheduler. Hidden from the user interface: a kill-switch, not a mode.
_Avoid_: engine mode, performance setting

**Animation ownership**:
The single operating-system window that owns the in-flight animated sequence, so that moving the pointer to another window cancels it.
_Avoid_: focus, active window

### Surfaces and platforms

**Platform**:
One of the three operating systems SmoothScroll supports: Windows (primary), macOS (maintained), Linux (maintained). The tier is stated intent rather than a runtime concept — enforcement is compile-time, through separate crates and per-OS build artifacts.
_Avoid_: target, OS abstraction

**App locale**:
A language shipped in the desktop application's own interface (currently 14). Independent of the site and installer lists.
_Avoid_: language, translation

**Site locale**:
A language shipped on the marketing site (currently 3). Independent of the app list: adding an app locale does not add a site locale.
_Avoid_: language, translation

**Installer language**:
A language available in the platform installer's interface (currently 3). Spelled differently from the other two lists and derived from neither.
_Avoid_: language, locale
