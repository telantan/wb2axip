## Demo designs

- [Demonstration AXI4-lite slave](demoaxi.v)
- [Demonstration AXI4(Full) slave](demofull.v)
  -- [AXI Addr](axi_addr.v) is a helper core used for calculating the "next" address in a burst sequence.  It's based upon [the algorithm discussed here](https://zipcpu.com/blog/2019/04/27/axi-addr.html).

- [Skidbuffer](skidbuffer.v)

## Crossbars

See [this post for a discussion of these
crossbars](https://zipcpu.com/blog/2019/07/17/crossbars.html).

- [AXI4](axixbar.v)
- [AXI4-Lite](axilxbar.v)
- [Wishbone](wbxbar.v)

These cores rely not only on the [skidbuffer](skidbuffer.v) listed above, but
also upon a separate [address decoder](addrdecode.v) that is common to all of
them.

All three cores are supported by the (dev branch of)
[AutoFPGA](https://github.com/ZipCPU/autofpga).

## Data movers/DMA enginse

- [AXIMM2S](aximm2s.v).  Supports unaligned transfers, but only fully aligned
  stream words.
- [AXIS2MM](axis2mm.v).  Doesn't yet support unaligned transfers.  It may
  eventually, but it will also only ever support full word transfers
  through the stream.
- [AXIDMA](axis2mm.v).  Unaligned transfer support is kept in another
  repository, under a different license (currently).

- [Synchronous FIFO](sfifo.v)
- [Synchronous FIFO with threshold](sfifothresh.v)

## Bus Bridges

- [AXI to AXI-lite](axi2axilite.v).  Supports 100% throughput even across burst
  boundaries, unlike other (similar) bridges of this type you might come across.
- [AXI-lite to AXI](axilite2axi.v).  A "no-cost" bridge.

- [AXI-Lite to Wishbone](axlite2wbsp.v)
  -- [Read side](axilrd2wbsp.v)
  -- [Write side](axilwr2wbsp.v)
  -- A following (optional) [arbiter](wbarbiter.v) will connect read and write sides together into the same WB bus.  Alternatively, each of the two sides can be submitted separately into a [WB Crossbar](wbxbar.v).


- [AXI4 Master to Wishbone](axim2wbsp.v)
  -- [Read side](aximrd2wbsp.v)
  -- [AXI4 Master to Wishbone](aximwr2wbsp.v)

- [Wishbone classic to pipelined](wbc2pipeline.v)
- [Wishbone pipelined to classic](wbp2classic.v)

- [Wishbone master to AXI4-Litejk slave](wbm2axilite.v)

- [Wishbone master to AXI4 slave](wbm2axisp.v)

  This core, together with its sister core, [migsdram](migsdram.v), form the
  basis for my approach to accessing DDR3 SDRAM memory through an AXI
  interface using Xilinx's MIG controller.  Other than the lag in going
  through the bridge, this solution works quite well--achieving full (nearly
  100%) bus throughput when loaded.

## AXI Simplifiers

These follow from the discussion in [this article about how to simplify
a bus interconnect](https://zipcpu.com/zipcpu/2019/08/30/subbus.html) into
something allowing easier integration of multiple slaves into a design.  They
work by aggregating the signaling logic across many slaves together.  See the
headers for each of the cores for the specifics of the standard subsets
they support.

- AXI Single, the companion to the [AXI Double](axidouble.v) core has not yet
  been written.  The design for it should be nearly identical.  The big
  difference between single and double AXI slaves is that single slaves can have
  only one address, and that address must always be available for reading.
- [AXI Double](axidouble.v).
- [AXI-Lite Single](axilsingle.v)
- [AXI-Lite Double](axildouble.v)

## Firewalls

The goal of these firewall(s) is to create a core that, when placed between the
bus master and slave, will guarantee that the slave responds appropriately to
the bus master---even in the presence of a broken slave.  If the slave is
broken, the firewall will raise a flag that can then be used to trigger a
logic analyzer of some type so you can dig deeper into what's going on.

As a bonus, the firewall may also have the capability of resetting the
downstream core upon any error, so that it might be reintegrated later into
the rest of the design for additional testing.

- [AXI4](axisafety.v)
- The Wishbone firewall is an (unfunded) work in progress.

## Clock domain crossers

- [AXI4 cross-clock domains](axixclk.v)
- [Asynchronous FIFO](afifo.v) -- support function