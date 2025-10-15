#programming #programming-philosophy #hacker-laws


The CAP Theorem (defined by Eric Brewer) states that for a distributed data store only two out of the following three guarantees (at most) can be made:

- Consistency: when reading data, every request receives the _most recent_ data or an error is returned
- Availability: when reading data, every request receives _a non error response_, without the guarantee that it is the _most recent_ data
- Partition Tolerance: when an arbitrary number of network requests between nodes fail, the system continues to operate as expected

The core of the reasoning is as follows. It is impossible to guarantee that a network partition will not occur (see [[../Hacker Principles/The Fallacies of Distributed Computing|The Fallacies of Distributed Computing]]). Therefore in the case of a partition we can either cancel the operation (increasing consistency and decreasing availability) or proceed (increasing availability but decreasing consistency).

The name comes from the first letters of the guarantees (Consistency, Availability, Partition Tolerance). Note that it is very important to be aware that this does _not_ relate to [_ACID_](https://github.com/dwmkerr/hacker-laws/tree/main?tab=readme-ov-file#TODO), which has a different definition of consistency. More recently, [PACELC](https://github.com/dwmkerr/hacker-laws/tree/main?tab=readme-ov-file#TODO) theorem has been developed which adds constraints for latency and consistency when the network is _not_ partitioned (i.e. when the system is operating as expected).

Most modern database platforms acknowledge this theorem implicitly by offering the user of the database the option to choose between whether they want a highly available operation (which might include a 'dirty read') or a highly consistent operation (for example a 'quorum acknowledged write').

Real world examples:

- [Inside Google Cloud Spanner and the CAP Theorem](https://cloud.google.com/blog/products/gcp/inside-cloud-spanner-and-the-cap-theorem) - Goes into the details of how Cloud Spanner works, which appears at first to seem like a platform which has _all_ of the guarantees of CAP, but under the hood is essentially a CP system.

See also:

- [ACID](https://github.com/dwmkerr/hacker-laws/tree/main?tab=readme-ov-file#TODO)
- [[../Hacker Principles/The Fallacies of Distributed Computing|The Fallacies of Distributed Computing]]
- [PACELC](https://github.com/dwmkerr/hacker-laws/tree/main?tab=readme-ov-file#TODO)