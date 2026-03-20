# AMCP Integration Guide

This guide explains how AMCP fits with adjacent agent standards.

## AMCP, MCP, and A2A

These standards address different layers of agent infrastructure.

- MCP: tool and data access
- A2A: agent-to-agent delegation and coordination
- AMCP: portable, long-term memory continuity

They are complementary.
None of them requires the others to exist.

## A simple mental model

Use the following framing when you need a short explanation:

- MCP connects agents to tools
- A2A connects agents to other agents
- AMCP connects agents to memory

That framing is useful for education and positioning, but it is not itself a normative part of the protocol.

## Example flow

1. An agent receives a user request.
2. The agent recalls prior context through AMCP.
3. The agent calls tools through MCP.
4. The agent delegates specialized work through A2A when needed.
5. The agent remembers the outcome through AMCP.

In that model:

- MCP handles action
- A2A handles coordination
- AMCP handles continuity

## Safe positioning guidance

Use these claims:

- AMCP complements MCP and A2A
- AMCP provides portable, long-term memory continuity
- AMCP helps complete the agent infrastructure stack

Avoid these claims unless independently verified:

- that a given ecosystem has officially adopted AMCP
- that A2A or MCP implementations are universally compatible with AMCP
- that AMCP is required for MCP or A2A systems

## Recommended public language

Preferred:

`AMCP helps complete the agent stack: MCP for tools, A2A for agents, AMCP for memory.`

Also acceptable:

`Build agents that access tools through MCP, collaborate through A2A, and preserve memory through AMCP.`
