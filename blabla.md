# Title Slide
Good afternoon.  My name is Philipp Hanslovsky and I am currently a graduate fellow in the lab of Stephan Saalfeld.  As a computational lab, our focus lies on the scalable reconstruction of neural circuits from large light and electron microscopy data. Today, I will present to you the metamorphisis of of our in-labe proof-reading and annotation tool BigCAT.


# Circuit Reconstruction
Let me first clarify what I mean when I speak of circuit reconstruction.  Electric circuits are represented by wiring diagrams that hold all the information necessary to understand the function of the circuit.  When it comes to neural circuit reconstruction of, in this analogy, neurons are the wires, and synapses are the wiring points.  In order to obtain such a wiring diagram of a nervous system, we therefore need to identify all individual neurons (wires) and the connections they form through synapses (wiring points).

<!-- I will begin with a broader overview of the problem of circuit reconstruction.  In general, circuit reconstructions aims to reconstruct all neurons of a nervous system from image data and find how these neurons are connected.  While purely manual reconstruction was used with great success and has produced meaningful scientific insight, it is a tedious effort that requires thousands of person hours or, in the case of more recent datasets, is not even feasible at all to begin with.  Based on the cost of generating ground truth for the CREMI challenge, we estimate the cost of reconstructing neurons from the FAFB dataset to be in the tens of billions of dollars.  The total endowment of HHMI might be enough to reconstruct FAFB, and that is only a single individual. -->

<!-- In order to extract meaningful information from this kind of data, there is no way around using automated machine learning algorithms and over the past years, performance of these algorithms has increased dramatically.  Typical machine learning workflows predict boundaries from image data.  Then, based on these boundary maps, pixels are grouped into supervoxels or fragments that for a reduction of the problem size.  Various agglomeration schemes are used to combine these fragments into larger objects, each of which (hopefully) represents one single neuron. -->

<!-- Still, this does not make the need for proof reading and visualization obsolete.  On the contrary, it is vital to adapt software to these changing requirements.  More precisely, we need to tools that allow us to -->
<!--  - generate ground truth data for the training of machine learning classifiers, -->
<!--  - visualize the data to evaluate the classifier predictions and find errors, -->
<!--  - guide the user to potentially corrupted areas, and -->
<!--  - give the user means to efficiently modify predictions where necessary. -->
<!-- It is essential that these tools scale to arbitrarily sized data.  Contrary to some other tools, we also want to enable heavy client side computations. -->
<!-- On top of these basic requirements, a collaborative approach to data modification is desirable, so users can work on the same data simultaneously. -->

<!-- # Existing Tools -->
<!-- There are great tools that cover some of these requirements.  I will name a few and explain why these tools did not satisfy all of our requirements. -->

<!-- ## BigCAT -->
<!-- I will start with the old BigCAT. The CAT in BigCAT is short for *colaborative annotation tool* and Big indicates the application to big data and BigCAT's heavy dependence on BigDataViewer. At its core, BigCAT is a slicing viewer that allows to look at the data arbitrary planes at arbitrary rotations.  On top of that, it offers a selection of painting tools (paint, flood fill), manual fragment agglomerations, and user annotations for synaptic connections.  The ground truth of the CREMI challenge was, in large parts, generated using this tool.  It lacks essential visualization tools (orthogonal views, 3D rendering) and is designed for a very specific use case and is extremely hard to extend or modify. -->

<!-- ## NeuTu -->
<!-- Should I even mention this? -->

<!-- ## Neuroglancer -->
<!-- Neuroglancer is developed by Jeremy Maitin-Shepard at google. It is a Javascript based visualization tool that runs in the web browser.  It visualizes image and label data in a set of three arbitrarily oriented orthogonal cross-sections.  3D meshes can be displayed in a 3D viewer if provided by a server, or generated from in-memory data in a Python process but not for arbitrary data sets.  In its basic version, it does not allow for any painting or agglomeration and it can only display two data types (number and argb).  Neuroglancer can be integrated in any web browser application, including jupyter notebooks.  However, this also limits memory use as well as threading and forbids any heavy client side computation unless starting another non-browser process that communicates with neuroglancer. -->

<!-- # BigCAT v2 -->
<!-- Recent development in BigCAT tries to combine what these tools offer and add what is not present.  As a recap, this is what we what we want to have in a our tool: -->
<!--  - Big data (mipmap sources) -->
<!--  - Orthogonal cross-sections in arbitrary rotations -->
<!--  - 3D Visualization -->
<!--    - Mesh Generation -->
<!--  - Multi-resolution Painting -->
<!--  - Semi automated agglomeration -->
<!--  - Annotations -->
<!--  - heavy client computations -->
<!--  - Extensibility -->
<!-- BigCAT is, right now, under heavy development, so not all of these are available yet, to some or full extent. I will explain the challenges that we are facing, and how solve them.  I will also show you some examples of what we can do already. -->

<!-- # Painting -->
<!-- Painting has been an essential feature of BigCAT since its inception.  Besides regular brush painting, BigCAT featured an array of special painting modes such as flood fill.  These painting actions would always be executed on the highest resolution layer, which renders painting impossible when zoomed out far in multi-resolution data.  The new BigCAT will enbale multi-scale painting.  This requires a more complex infrastructure with three layers:  At the bottom, the label data form the foundation or background.  A canvas of the same size as the input data is stacked on top of the background and initialized to be transparent.  Whenever the user triggers a painting event,  a binary mask layer is added over the canvas.  Every pixel that gets painted is represented by a `true` value in the mask.  At the end of every painting action (e.g. mouse release), the canvas flushes its contents into the canvas at the appropriate scale level and is discarded.  Once flushed into the canvas, the data is propagated to other scale levels, if applicable. The user has the option to persist the canvas into the backgroundc layer at any time.  It is the responsibility of the input data to offer means to do so.  For a local file that just means that the data are persisted to disk.  In a collaborative environment, this can mean that the changes are committed to a separate branch, or potential conflicts are consolidated. -->


<!-- # 3D mesh generation -->
<!-- Typically, meshes for 3D visualization are computed offline and provided to the client by a server.  This way, meshes can be pre-computed and the client is not required to execute heavy number crunching.  The downside of this is that the client cannot generate meshes when there is no server to provide them, or the label data change, e.g. through painting. -->

