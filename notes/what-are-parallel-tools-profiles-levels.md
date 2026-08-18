# What are the parallel tools, profiles, and levels?

> Chapter 5 · Day 11

## One-line answer
Parallel tools (Slices, Tiles, Wavefront/WPP) let encoders/decoders use many cores at a small cost to compression efficiency; profiles/tiers/levels are the conformance vocabulary describing which tools, which bitrate ceiling, and how much resolution·fps·bitrate a decoder must support.

## Key points — parallel tools
- **Slices:** independently-decodable segments (error resilience/packetization).
- **Tiles:** rectangular grid regions, decodable independently (good load balancing).
- **WPP:** parallel rows of CTUs, each lagging the row above → keeps most CABAC context → smaller efficiency loss.
- Tradeoff: more parallelism → less compression efficiency (broken dependencies).

## Key points — profiles/tiers/levels
- **Profile** = which tools (Main = 8-bit; **Main 10** = 10-bit, common for HDR).
- **Level** = how much (e.g. 5.1 ≈ 4K@60). **Tier** = Main vs High bitrate ceiling.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
