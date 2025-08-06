---
applyTo: '**'
---
# Simple ModuLink-py Debugging Example
cd /Users/joshuawink/Documents/github/mcp/overview-to-tasks && python -c "
import asyncio
from modulink import Chain
from prompt.prompt import format_for_llm

class Debug:
    def after(self, name, result, pos, i): print(f'{name}: {list(result.keys())}')

async def test():
    chain = Chain(format_for_llm)
    chain.use(Debug())
    result = await chain.run({'key_sections': {'task': 'test'}})
    print(f'Final: {result.get(\"system_prompt\", \"None\")}')

asyncio.run(test())
"