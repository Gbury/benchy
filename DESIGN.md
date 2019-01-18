
TODO: Lots of things !


Basic notions
=============

* Artifact:

  - An artifact is something that can be used in computations,
    it is an abstraction over static content contained in text files

  - frogutils maintain a database of its known artifacts

  - each artifact is associated to its hash:
    + this has acts as a sort of version number
    + this allows to detect when an artifact has changed

  - an artifact can typically be:
    + an input file
    + the result of a previous computations

  - frogutils maintain a database of its known artifacts
    + contains a partial mapping from reasonable identifiers (e.g. tptp:ARI/ARI001-1.p)
      to the hash of the ressource (artifacts resulting from computations might not have
      a "reasonnable identifier")
    + contains a full mapping from hash to contents
    + artifacts contents might be symlinks (to avoid duplicating problem files)
    + artifacts contents might need to be compressed locally to save space

* Ressource:

  - A ressource is the source of computations.

  - Most notably, provers (together with their configuration, and list of outputs)
    are ressources

  - Just as for artifacts, ressources are hashed to detect changes and uniquely identify them

  - ressources are the basis of computations

  - ressources have inputs and outputs, each of these is labbelled (e.g. "input_pb", "stdout",
    "stderr", "profile_info", "statistics", "formal_proof", "model", ....)

* Event:

  - An event is the result of applying a ressource to some artifacts (e.g. running a prover)

  - Events record the identifiers of the ressource used, as well as all input/output artifacts

  - Each event records statistics (such as real time, system time, cpu time, memory,
    date, characteristics of the machine, etc...)

  - frogutils maintain a database of all known events


* Computation

  - a computation is a directed bipartite graph where each ndoe is either an unnamed artifact
    or a ressource: ressources have input edged coming from artifacts and output edges to other
    unnamed artifacts. Each edge is labelled according to a ressource's labelling of inputs/outputs

  - these graphs effectively describes a computation with inputs (i.e. artifacts nodes which are not
    the destination of an edge), and which creates artifacts (all artifacts ndoes that are not inputs)

  - example of computation:
                                smtlib           ------         proof_coq              ---------   stdout
                              ---------- < > ----| z3 |       ------------ < > ------- | coqc  |----------- < >
                             /                   ------      /                         ---------
                            /                               /
                 -----------  tptp               -----------    proof_dk               --------    stdout
    input_1 ---- |  Why3   |------------ < > ----| archsat | ------------- < > ------- | dk   | ----------- < >
                 -----------                     -----------                           --------
                            \                               \
                             \    zf             ----------  \    statistics
                              ---------- < > ----| zipper |   \----------------- < >
                                                 ----------


* Test/Benchmark specification:

  - A test or benchmark is comprised of a set of pairs each containing:
    + a computation graph
    + a list of inputs (each elements of this list should contains as many elements as the required number of inputs of the computation)

* Test/Benchmark results:

  - A test or benchmark is a set of events (possibly ordered according to some ordering ?), resulting from the evaluation
    of computations on inputs

  - Since events are maintained in a global database, a benchmarks result is really just a list of event ids


Features/workflow
=================

* Configuration:

  - Base Artifacts and Ressources must be specified in a configuration file

  - Benchmarks might be specified in separate configuration files,
    each benchmakr has a name

  - benchmarks should specify on which machines they want to be run


* Running benchmark:

  - as simple as 'frog bench <benchmark_name>'


* Server/client architecture:

  - Allow instances of frogutils on different machines

  - central server to which all client can connect (over tcp ?)

  - server receives global commands such as running benchmarks

  - the dispatch to the clients according to the benchamrk configuration,
    and the clients capabilities (e.g. some client may have some ressources installed,
    or artifacts already available, while others don't)

  - ideally: one client per machine able to run computations

  - the server might run concurrently with a client on the same machine
    (communications are still tcp between them though)

  - artifacts should be transferable (i.e. a prover might run on some machine,
    and its output checked on another).
    -> centralize all artifacts on the server machine ?
    -> or transfer some artifacts from one client to the other on demand ?


* Using results

  - Parts of the internal databases should be exportable and importable,
    particularly, a benchmark's results should be easily exportable in
    a single file, possibly including all artifacts (if required)

  - Analyzing results: given that the format of the results is highly
    dependant on the way benchmarks are run, and the results are stored
    in a format specific to frogutils, developpement of analyze tools should
    be done together




