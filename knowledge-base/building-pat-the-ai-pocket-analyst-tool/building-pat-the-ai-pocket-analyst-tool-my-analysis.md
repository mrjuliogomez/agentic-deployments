# Bridgewater's Pocket Analyst, and what an operator takes from it

Bridgewater's applied AI team gave a talk at Interrupt 2026 about PAT, an internal research agent hundreds of their people now use every day. It does in minutes the deep analytical work that used to take a human days. I watched it twice and pulled it apart. This is what stood out, written for people who build agents rather than people who write about them.

One note before we start. Bridgewater calls their in-house research staff "investors". To everyone outside the firm that word means a client or someone buying shares, which is not what they mean. Read it as trader or research analyst throughout. That is who uses PAT.

## The one idea worth stealing

They stopped treating the coding agent as an agent. Santi, their tech lead, comes from compiler theory and he reframed the whole thing. A compiler turns code into something lower level, reliably and the same way every time. Their coding agent turns a plan into Python the same way. No agentic orchestration deciding what to do next, just a detailed plan that compiles. Two runs of the same plan produce the same code 95 percent of the time. That is the gap between a good demo and something a firm trusts with real money.

## What made it click for me

**The plan is the analysis.** They spend heavily upfront turning a vague question into a precise plan, with every dataframe, schema and dependency named. Once the plan is good, execution is almost mechanical. Most of us underinvest in planning and pay for it later.

**Two agents, not one.** A chat agent that talks like a trader and never shows code, and a coding agent that only writes code. Keeping them apart means neither carries the other's baggage, and each gets sharper on its own job.

**Domain context is the moat.** They let their traders and analysts write the context alongside the developers. Their words, their jargon, their frameworks. The model sounds like a colleague because a colleague taught it.

**A search agent that thinks like a human.** Instead of matching a data series by name, it checks the frequency, the units, and whether the numbers match what a researcher would expect. That one change took their search accuracy from about 50 percent to 90.

## The flywheel is the real product

The single feature I keep thinking about is the learning loop. When PAT gets something wrong, the analyst clicks one button. An agent then reads the conversation, writes a failing test that reproduces the mistake, fixes the underlying context until that test passes, checks it broke nothing else, and opens a pull request. A human approves it. The next person to ask a similar question gets the smarter version out of the box.

Sit with that. Every single use is a chance to improve the tool for the whole firm, and the improvement is captured automatically instead of living in one person's head. Most teams treat feedback as a comment someone might read later. Bridgewater turned it into a test, a fix, and a pull request. That is how a tool compounds instead of plateaus.

## What I am taking into my own work

I build agentic systems for narrow, real workflows, not generic assistants. PAT is the strongest argument I have seen for that approach. Specialise the agent, benchmark it hard, and only then compound. The takeaway I keep coming back to is theirs, not mine. Avoid generic powerful agents. They make great demos and terrible daily tools.

## Sources

Official recording, LangChain Interrupt 2026: https://www.youtube.com/watch?v=lXZb21CfeIY

Bridgewater AIA Labs write-up: https://www.bridgewater.com/aia-labs/how-bridgewaters-aia-labs-built-pat-the-ai-pocket-analyst-tool

Surfaced via Linas Beliunas on LinkedIn
