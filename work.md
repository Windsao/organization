# Lectures and Questions

The sections of the this document are either:

- **Lx** in which case they refer to a lecture, and provide a set of questions, or
- **Cx** in which case they references to code-bases, and include a set of questions associated with it.

References to "the book" refer to [this](https://github.com/gwu-cs-advos/advos_book).

## L0: Reading Code

No work needs to be done before this class!
However, what we'll cover is likely the most important part of the whole class.
If you only read one part of the material for the class, read this.

References:

- See Sections on "Reading Code" and "Writing Readable Code" in the book.

## L1: Constraining System Complexity

Remember that you're expected to share questions you have in the feedback, so make sure to write down your questions as you go through the videos.
Please watch the lecture video on [constraining complexity](https://youtu.be/a8V2d33KvaE).

Questions (complete in the provided form):

1. Give a few examples of modularity in desktop operating systems?
2. Give an example of essential and accidental complexity in code you've written (briefly), and explain why you separate essential and accidental for that task?
3. It is counter-intuitive that abstractions constrain the more chaotic behavior of lower-level interfaces, into higher level APIs.
	Thoughts on this?

References:

- Lecture video on [constraining complexity](https://youtu.be/a8V2d33KvaE).
- See Sections on "Complexity Management" in the book.

## C1: Reading Documentation, Event Management, and Zircon

This week is an exceptional week:
We have to get through a lecture before the next class.
It is necessary to understand the code in Zircon.
I lightened the code investigation load.
Question about the video on [system design concerns](https://youtu.be/dX3-pUvWnOk):

1. Why do you think that the system design concerns are seen more in system modules than in applications?
	For example, you might not care about parallelism, concurrency, nor naming in your application, but you must consider them in system modules.

How do you form your initial hypothesis about code-bases, and your initial goals?
Often, you start with the design documents, and documentation.
Lets start out "easy mode" and dive into a project with nice documentation.
Lets dive into the concurrency challenges discussed in class in google's new OS, Fuchsia!

Questions (complete in the provided form):

1. Describe signals and their relation to events.
2. What functions enable threads to block waiting for multiple concurrent events?
3. List some system objects that can generate events.

References:

- Lecture:

	- Lecture video on [system design concerns](https://youtu.be/dX3-pUvWnOk).
	- See Section "System Interface Design Considerations" in the book.

- Fuchsia:

	- [Fuchsia documentation ](https://fuchsia.dev/fuchsia-src/concepts).
		Starting point help: We want to focus on the *kernel*'s abstractions.
		You can find the documentation for the kernel on this page as well.
		You can start by understanding what calls are blocking, and which are non-blocking, then move on to how the zircon notion of *signalling* is tied to events.
	- Some of the [conventions](https://fuchsia.dev/fuchsia-src/concepts/api/system) make digesting the documentation easier.

## L2: Interface Properties

Today we're going to understand a number of properties of interfaces that we can use to understand what differentiates good interfaces, from those that are more difficult to use.
Keep in mind that there are no perfect interfaces out there, so all of this should be taken as a modus operandi.
Watch the [lecture](https://youtu.be/mKJcqvozfA8) and answer the questions below (remember: the form is linked from Piazza).

Questions (complete in the provided form):

1. Explain why locks don't compose.
	Does this mean that we should not use locks?
2. File `mmap` and `read`/`write`/`lseek` are not orthogonal.
	They both enable the user to modify file contents.
	`mmap` came after the other file access functions.
	Why do you think adding `mmap` was worth it over concerns of orthogonality?
3. Provide at least one example of code you've seen or written that is a good example of some of these properties (either expressing these properties well, or not well).

References:

- Lecture [video](https://youtu.be/mKJcqvozfA8).
- See Section on "Interface Design Properties" in the book.

## C2: Concurrency on Servers

We're going to learn more about event notification APIs by diving in to `libuv` or the `demikernel` API.
You should choose *one* of these systems to dive into.
The intention is that your group will have people to cover both APIs.
These are both systems that handle concurrency in different ways.
`libuv` is the library underlying `node.js` and exposes a callback model to C code.
It is cross-platform and we can focus mainly on the POSIX/Linux part of it.
The `demikernel` (DK) is a "library operating system" that avoids isolation, and instead provides a programming model much loser to the raw devices.

Questions for `libuv` (complete in the provided form):

1. What APIs are necessary for a client to read from a network socket?
	Tests are likely going to be quite helpful for this.
2. What Linux system calls are used for event multiplexing (which functions are they called from, where in code, etc...)?
	You need to be specific here, thus dive deep into the code.

Question for DK:

1. What APIs does the DK use to issue requests to send or receive data, and get notified when there is an event?
	What APIs does can a client use to "block" awaiting events?
2. DK does not *actually* block (despite having APIs labeled as blocking).
	I define blocking as "being removed from the scheduler runqueue to allow other threads to execute".
	Provide evidence of this in the code.
	Why do you think this is?

References:

- [libuv](https://github.com/libuv/libuv).

	- Starting out by perusing the [tests](https://github.com/libuv/libuv/tree/v1.x/test) will give you a lot of context to dive in.

- Demikernel:

	- [Demikernel -- see Fig 3](https://irenezhang.net/papers/demikernel-hotos19.pdf) abstractions and API.
	- [API](https://github.com/gwu-cs-advos/demikernel/blob/master/API.md) documentation

## L3: Capability Based OS Design I

Today we'll briefly play a mind game: What can we do to nearly minimally abstract hardware resources into a useable set of abstractions with which to build modular systems?
Watch the video, and answer the following questions.
I highly recommend that you read the chapter (in references) in the book.
It includes many definitions and more specific discussions to be more concrete than the recording.
There's also a fun analogy with high-performance jets.

Questions (complete in the provided form):

1. Where does a semantic gap exist between the abstractions provided by the discussed resources and some set of higher-level requirements?
    What would the system have a hard time doing?
2. What are the downsides of this design?

References:

- Lecture [video](https://youtu.be/aWHZaYwWRTM).
- See Section on "Example OS Design: Composite" in the book.

## C3: Composite Runtime

Lets dive into what the code looks like to actually create, modify, and use the kernel resources discussed in the previous video.
We're going to dive into the API for allocating and using those resources.
Your first project will use this API to implement the core of an RTOS, so time that you put into it now will pay off later.

Questions (complete in the provided form):

- Does this API create processes (i.e. components) in a manner closer to `posix_spawn` and `CreateProcessA`, or closer to `fork`?
- Can you summarize tersely why there are so many thread and "receive" APIs (see below what the `rcv` resources are)?
- What API enables the creation of shared memory?

References and hints:

- The [Composite Runtime](https://github.com/gwsystems/composite/tree/loader/src/components/lib/crt) (`crt`) attempts to provide a usable API to program the discussed resources.
	The names aren't always consistent with the video.
	A mapping:

	- *asynchronous activation endpoints* have a `rcv` that threads can suspend upon, and an `asnd` which other threads (and interrupts) can use to activate the thread associated with the `rcv`.
	- *synchronous invocation call-gate* are `sinv` (synchronous invocation) and `sret` (synchronous return).

- For now, I'd ignore the blockpoint, lock, and channel support.
- The system's documentation might be a good place to start (particularly sections 1.3 and 2 in the [dev manual](https://github.com/gwsystems/composite/blob/loader/doc/dev_manual/composite_dev_manual_v2020-07-04.pdf)), but the book for this class (especially the chapter "Example OS Design: Composite") covers the abstractions in a lot more detail.
- The API is best documented in [crt.c](https://github.com/gwsystems/composite/tree/loader/src/components/lib/crt/crt.c), and an example of its use can be seen in the system [constructor](https://github.com/gwsystems/composite/blob/loader/src/components/implementation/no_interface/llbooter/llbooter.c).
- You will *not* be able to understand the `cos_kernel_api` nor `cos_defkernel_api` APIs with the information you have, currently -- the resource table construction and retyping is confusing.
	You'll learn about this in L4.
	For now, view them as a wrapper around the kernel system call API that handle resource table allocation.

## L4: Capability Delegation and Revocation

Diving deeper into microkernel construction, lets address *why* you want to have simple user-level abstractions to compose protection and access hardware.
We need to enable complex systems to be built from various levels of mechanism and policy composed into higher-level abstractions.
This requires programmatically being able to define the subsets of resources that each module should have access to.
Fundamental to this in capability-based systems is the notion of how resources can be made accessible (the capability delegated) to other modules, and how they can later be revoked.

Questions (complete in the provided form):

1. Why is delegation necessary?
2. What are the trade-offs with revocation being recursive (as presented), or simply making revocation only remove the specific delegated capability (not none of the delegations made from there)?

Please focus on spending time providing good questions for this lecture.
I want the feedback to understand where confusions exist.

References:

- Lecture [video](https://youtu.be/krvJS01IrXE).
- See Section on "Capability-based Protection and Delegation and Revocation" and "Component-based Design on Composite" in the book.

## C4: Using the `crt` for System Construction

Read through:

- the [constructor](https://github.com/gwsystems/composite/blob/loader/src/components/implementation/no_interface/llbooter/llbooter.c) (whose job is to create the other system components), and
- the [capability manager](https://github.com/gwsystems/composite/blob/loader/src/components/implementation/capmgr/simple/capmgr.c).

These are the main users of the `crt` library as they manage the capability tables of themselves, and of other components.
There are a lot of rabbit holes to go down here.
Remember, three hours is enough ;-)

Questions (complete in the provided form):

- What's your hypothesis for what the `args_*` API is all about?
	You will **not** be able to have 100% certainty about this.
- Tersely describe (in as high of terms as possible) what the loader is doing, step by step.
- What aspects of the capability manager don't match your mental model (following class discussion) about what you'd expect the capability manager to do?

## L5: User-level Management of Kernel Memory

Finally, we'll address the question, "how do we allocate kernel data-structures, and control the access rights to the system's memory"?
The mechanisms behind this are highly unintuitive, and very clever.
Lets dive into memory retyping and the user-level management of kernel memory.
Questions (complete in the provided form):

1. Summarize the motivations for the discussed design?
	Why not use a more conventional approach?
2. Summarize your understanding of why kernel integrity is ensured using the discussed design.

References:

- Lecture [video](https://www.youtube.com/watch?v=b1ARRd3b8dY).
- See Section on "Memory Management" in the book.

## C5:

Questions (complete in the provided form):

- TBD

References:

- [TBD](TBD).
	Starting point help: TBD.

## L6:

Questions (complete in the provided form):

- TBD

References:

- Lecture [video](TBD).
- See Section on "" in the book.

## C6:

Questions (complete in the provided form):

- TBD

References:

- [TBD](TBD).
	Starting point help: TBD.

## L7:

Questions (complete in the provided form):

- Lecture [video](TBD).
- TBD

References:

- See Section on "" in the book.

## C7:

Questions (complete in the provided form):

- TBD

References:

- [TBD](TBD).
	Starting point help: TBD.

## L8:

Questions (complete in the provided form):

- TBD

References:

- Lecture [video](TBD).
- See Section on "" in the book.

## C8:

Questions (complete in the provided form):

- TBD

References:

- [TBD](TBD).
	Starting point help: TBD.

## L9:

Questions (complete in the provided form):

- TBD

References:

- Lecture [video](TBD).
- See Section on "" in the book.

## C9:

Questions (complete in the provided form):

- TBD

References:

- [TBD](TBD).
	Starting point help: TBD.

## L10:

Questions (complete in the provided form):

- TBD

References:

- Lecture [video](TBD).
- See Section on "" in the book.

## C10:

Questions (complete in the provided form):

- TBD

References:

- [TBD](TBD).
	Starting point help: TBD.

## L11:

Questions (complete in the provided form):

- TBD

References:

- Lecture [video](TBD).
- See Section on "" in the book.

## C11:

Questions (complete in the provided form):

- TBD

References:

- [TBD](TBD).
	Starting point help: TBD.

## L12:

Questions (complete in the provided form):

- TBD

References:

- Lecture [video](TBD).
- See Section on "" in the book.

## C12:

Questions (complete in the provided form):

- TBD

References:

- [TBD](TBD).
	Starting point help: TBD.

## L13:

Questions (complete in the provided form):

- TBD

References:

- Lecture [video](TBD).
- See Section on "" in the book.

# Homeworks

IoT systems are increasingly popular.
Many of them forego security and isolation for simplicity.
Lets build a secure RTOS to control the physical world in a trustworthy manner!

The *final* product will have the following features:

- Multithreaded with communication and synchronization mechanisms between threads including

	- thread creation and prioritization
	- `go`-like channels
	- locks for critical sections

- An isolation-providing process-based abstraction for applications

- A nameserver that enables processes to

As we're implementing about an RTOS, we should keep it as simple as possible, avoid generalizations, and avoid dynamic memory allocation.

## RTOS

Lets start by creating the core services for a RTOS in a *single protection domain* (i.e. in a single component).
These services include:

1. threads,
2. preemptive, priority-based scheduling,
3. timers so that threads can block for periods of time,
4. blocking locks (not spinlocks), and
5. channels to pass data from a producer to a consumer.

## RTOS + Application Isolation

Application threads should be in a separate protection domain than the core RTOS.
They will use synchronous invocations to communicate with and make requests from the RTOS component.
We'll make a simplifying assumption that only a single application thread executes in the application's component (i.e. that they are single-threaded).
To communicate with the RTOS component, this requires that we set up shared memory between the application and the RTOS to pass arguments, and get return values.

## Extensibility

Lastly, we're going to enable applications to register themselves as the providers of specific services in a namespace.
Implement a simple hierarchical namespace that applications can register themselves in, and other applications can "open" to communicate via channels with the service.
This will enable the system to be extended with functionality and services as applications "hook themselves in" to the shared namespace.
