# AI_LOG.md

Student Name: Syed Subhan Hassan
Student ID: 906732940
Project / Milestone: M3 - Qt AI Workspace

---

## Part 1: Summary of AI Tools Used

Tools Used: Claude

Scope of Assistance: I implemented the GUI architecture, event-driven slot handlers, Qt widget layouts, custom model adaptation, and backend integration for Milestone 3 myself. This included constructing the primary layout hierarchy for the main application window, individual widget components, custom table models, the polymorphic reset interface, widget signal/slot connections, and component event verification.

I used Claude as a technical assistant and debugger to handle edge cases, resolve Qt framework nuances, and troubleshoot C++/Qt lifetime and signal propagation patterns:
1. Debugging model/view synchronization issues and table layout glitches when updating dynamic datasets.
2. Resolving signal/slot connection signatures and ensuring safe component communication without using lambdas, as required by the specification.
3. Managing state consistency invariants (e.g., ensuring corpus invalidation automatically disables backend operations and clears stale view data across widgets).
4. Implementing the non-Qt function pointer callback pattern alongside Qt's event loop and modal confirmation dialogs.
5. Troubleshooting stylesheet rules for non-white widget background requirements on title headers, Reset, and Exit buttons.
6. Verification testing of polymorphic dispatch across the various GUI component types.

---

## Part 2: Detailed Log of Substantial AI Assistance

### Component: Table Model Data Synchronization and Dataset Updates

What did the AI do? I consulted Claude on how to best update the underlying data container within a custom table model so that the table view reflects changes cleanly without crashing or leaving phantom selections.

What did the AI get wrong / What was the flaw? Claude initially recommended manually triggering layout or data change notifications without proper encapsulation boundaries. However, altering the size of the underlying data structure directly caused the table view to retain invalid model indexes, resulting in out-of-bounds view accesses and segment faults when selecting rows after a dataset size change.

How did you fix/modify it? I discarded the flawed update suggestion and implemented strict model reset boundaries around data replacement operations. This properly notifies all connected table view observers to invalidate selection states and rebuild internal index maps safely. I verified this by testing rapid operations with varying dataset sizes and confirming that no dangling indexes or invalid row selections persisted.

---

### Component: Exit Callback Integration and Modal Confirmation

What did the AI do? The M3 specification requires storing a function pointer callback and invoking it when the Exit button is clicked. I consulted Claude on how to wire the Exit button click slot to execute this callback while ensuring clean application shutdown.

What did the AI get wrong / What was the flaw? Claude suggested connecting the Exit button signal directly to immediate application termination and calling the function pointer inside a destructor. This was incorrect because it bypassed the required user confirmation step; the window would close before the user had a chance to select an option in the confirmation dialog.

How did you fix/modify it? I implemented a dedicated member slot for the exit action. When triggered, it checks if the callback is registered and invokes it. I implemented the standalone exit callback function to display a modal question dialog. If the user cancels or selects "No", termination is prevented and the application state remains entirely untouched. I verified this by testing both user choices during a live run.

---

### Component: Polymorphic Application Reset via Interface Inheritance

What did the AI do? The specification mandates using an abstract interface polymorphically to reset major GUI components. I asked Claude for guidance on how the main window should store and iterate through these components.

What did the AI get wrong / What was the flaw? Claude suggested casting concrete widget pointers using dynamic downcasting inside the reset slot handler. Relying on dynamic downcasting at runtime is an anti-pattern that circumvents static type safety and violates the intended design of storing polymorphic interface pointers.

How did you fix/modify it? I declared a collection of base interface pointers inside the main window class. In the reset handler, I iterated over the base pointers using virtual dispatch. Each concrete widget overrides the virtual reset method to clear its internal line edits, models, sliders, and labels to default startup values. I verified this by populating data, executing processes, adjusting inputs, and clicking Reset—confirming all widgets returned strictly to their startup state and the status bar updated correctly.

---

### Component: Cross-Widget Corpus Invalidation and State Consistency

What did the AI do? When a user adds a document, the current backend corpus becomes stale. I consulted Claude on managing state propagation across widgets so that dependent functional buttons disable themselves until the corpus is rebuilt.

What did the AI get wrong / What was the flaw? Claude initially suggested having the document management widget directly call state-change functions on peer widgets by passing child widget pointers directly. This tightly coupled independent GUI components and broke encapsulation boundaries.

How did you fix/modify it? I enforced decoupled signal/slot communication routed through the central main window:
1. The document management widget emits a custom signal when a new file is loaded.
2. The main window catches this signal, marks the internal corpus state as invalid, updates the status text, and triggers update routines across dependent widgets.
3. The dependent widgets disable their respective action buttons and clear stale result tables and inspector displays.

I verified this invariant by adding additional documents after performing operations, confirming that all results immediately cleared and action buttons disabled until processing was explicitly re-run.

---

### Non-code Assistance (Styling Requirements & Build Setup)

I also used Claude to review styling stylesheet syntax to ensure compliance with the non-white background requirements for the application header area, Reset button, and Exit button. Claude helped format clean style selectors with high-contrast text to ensure full readability while meeting visual presentation requirements. Furthermore, I verified that build configuration files correctly handled automated meta-object compilation and linked the required graphical components.

---

## Part 3: Student Verification Statement

I affirm that I have thoroughly reviewed all AI-assisted code listed above, that I fully understand how it operates within the broader application architecture, and that I am able to modify or debug it independently.
