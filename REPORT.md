# Experiment 4 Phase 1 Results

The RISC-V SE workload reads the unchanged element and bitmap trace
formats, runs the C kernels, and validates every output against the same
dense golden output. No `vbstacc` instruction is implemented in this phase.

CPU model: `TimingSimpleCPU`.

| Batch | Mode | Measurement | Instructions | Cycles | Speedup vs dense | Descriptor accesses | Operand selections | Accumulator updates |
|---:|---|---|---:|---:|---:|---:|---:|---:|
| 1 | dense | gem5_measured | 123,925,371 | 177,947,945 | 1.000x | 0 | 17,694,720 | 17,694,720 |
| 1 | element | gem5_measured | 169,132,539 | 242,681,521 | 0.733x | 11,789,978 | 11,789,978 | 11,789,978 |
| 1 | bitmap | gem5_measured | 162,919,577 | 223,246,448 | 0.797x | 2,211,840 | 11,789,978 | 11,789,978 |
| 8 | dense | linear_projection_from_batch1 | 991,402,968 | 1,423,583,560 | 1.000x | 0 | 141,557,760 | 141,557,760 |
| 8 | element | linear_projection_from_batch1 | 1,353,060,312 | 1,941,452,168 | 0.733x | 94,319,824 | 94,319,824 | 94,319,824 |
| 8 | bitmap | linear_projection_from_batch1 | 1,303,356,616 | 1,785,971,584 | 0.797x | 17,694,720 | 94,319,824 | 94,319,824 |
| 32 | dense | linear_projection_from_batch1 | 3,965,611,872 | 5,694,334,240 | 1.000x | 0 | 566,231,040 | 566,231,040 |
| 32 | element | linear_projection_from_batch1 | 5,412,241,248 | 7,765,808,672 | 0.733x | 377,279,296 | 377,279,296 | 377,279,296 |
| 32 | bitmap | linear_projection_from_batch1 | 5,213,426,464 | 7,143,886,336 | 0.797x | 70,778,880 | 377,279,296 | 377,279,296 |
| 128 | dense | linear_projection_from_batch1 | 15,862,447,488 | 22,777,336,960 | 1.000x | 0 | 2,264,924,160 | 2,264,924,160 |
| 128 | element | linear_projection_from_batch1 | 21,648,964,992 | 31,063,234,688 | 0.733x | 1,509,117,184 | 1,509,117,184 | 1,509,117,184 |
| 128 | bitmap | linear_projection_from_batch1 | 20,853,705,856 | 28,575,545,344 | 0.797x | 283,115,520 | 1,509,117,184 | 1,509,117,184 |

## Key findings

- Bitmap is `1.087x` faster than element-wise sparse 
  and reduces descriptor accesses by `81.240%`.
- Bitmap remains `1.255x` slower than dense in the C 
  kernel because software descriptor decode dominates the saved ternary
  accumulator updates.
- Phase 1 therefore validates the block format advantage over element-wise
  sparse execution, while also showing why phase 2 needs `vbstacc`.

All four batches execute natively with `mismatches=0`. Batch 1 is
measured in gem5 TimingSimpleCPU. Because the phase-1 kernel is a
sample-major sequence with no cross-sample reuse, batches 8/32/128
use a declared linear projection from that measured ROI; they are not
presented as independently simulated cycle measurements. Loading and
golden checking are outside the ROI bracketed by gem5 reset/dump
pseudo-operations.
