---
layout: content
menu: user-manual
title: Performance
---

# Performance

Below are the results of a simple [https://github.com/jhalterman/modelmapper/blob/master/core/src/test/java/org/modelmapper/performance/DozerComparison.java micro-benchmark] that compares the performance of ModelMapper to that of [http://dozer.sourceforge.net Dozer]. While performance on the JVM is notoriously difficult to measure, these results serve as a general comparison between the two.

### Methodology

 * The [https://github.com/jhalterman/modelmapper/blob/master/core/src/test/java/org/modelmapper/performance/DozerComparison.java micro-benchmark] starts by instantiating the mappers and performing a warmup mapping to initialize internals.
 * 10 sets of 100,000 map operations are performed by ModelMapper and Dozer respectively.
 * 10 sets of 10,000 concurrent map operations across 10 threads are performed by ModelMapper and Dozer respectively.

### Results

Following are the results for each of the 10 passes, captured as **map operations per second** (higher numbers = faster):

||Pass||ModelMapper||Dozer||
||1||43,859||13,498||
||2||45,228||13,674||
||3||45,766||13,676||
||4||45,475||13,612||
||5||45,475||13,674||
||6||45,516||13,721||
||7||45,662||13,689||
||8||45,310||13,548||
||9||44,903||13,553||
||10||45,105||13,568||

For the concurrent test the results are as follows:

||Pass||ModelMapper||Dozer||
||1||7,022||2,339||
||2||7,923||2,391||
||3||7,880||2,181||
||4||6,816||2,319||
||5||7,662||2,295||
||6||7,886||2,279||
||7||7,627||2,181||
||8||7,462||2,187||
||9||7,727||2,325||
||10||7,374||2,293||