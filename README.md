# AI-Experiments
List of experiments developed with/by AI

# Vibecoding
Chatbot + copy pasting and lot of sweaaring with manual fixes. Not on refactoring or restructuring much, just fixes with duct tape to make it work.

## [PI Chat](https://github.com/zcat-app/pi-chat)
Real vibecoding project, based on chat interaction and copy pasting solution. No structure just chat without context management and huge pain since the PI Chat was also dealing with reasoning. 
That means used tool (NanoGPT) was strugling hard with printing actual resoning from model with reasoning 'container' tags inside of the PI Chat. 

### Goal: 
Create chat app that can be intereacted by text & voice and can get text & voice responses. To be able to run on rasberry (very slow), laptop or vps.

### Tools: 
- Java 25, Quarkus, docker, jextract, Wisper.cpp (STT) + Silero VAD, Piper (TTS), Llama.cpp, websockets, 
- NanoGPT - mostly GPT-5.4, Opus 4.6 for dockerization issues

### Result
I had to prompt, extract broken answers (reasoning container problem) and manually fixing smaller parts. I touched some parts of code but mainly just bug-fixing, I did not try to refactor. 
I was able to run the app on Raspbbery PI 5 (16GB RAM), VPS (4 CPU, 16 GB RAM - Hostinger) and personal laptop

# Agentic development
My view on AI was changed after couple of NDC and Devoxx talks where a couple of companies claimed it helped them significantly in
some specific task. Later the hype of replacing developers hit the net, so I decided to give a couple of shots. I tried some customized version of Spec-driven development - creating spec, design decisions,
taks/tickets and implementation lifecycle with possible parallelization (in somce case, with worktrees)

## [Zecsite](https://github.com/zcat-app/zecsite)
Zecsite was my second project (but first finished) to try agentic coding. The idea was based on my own experiment with Chart.css and Reddit post about using CSS to achieve JavaScript like behavior. And
since JavaScript is hell and base for multiple exploits, I wanted to try to create static HTML website without Javascript that might not suck. And it might be cool to create onion service (Tor) and eepsite (i2p), too.
IPFS for fun, when I will forger to extend domain.

For this I decided to create website about Zcash. Privacy focused crypto currency that seems on of very few projects that contributes by research, work and donations to open source community. And surprise, surprise,
datk net shows no love to it, nor I was aware of any website that would try to introduce it there (might be, I know only Dred - Reddit like forum). So Zecite collects data - posts, news, chain stats from the net and
generate static HTML with CSS (with graphs and themes) that is byte identical (should be) and it is accessible via clearnet (https), onion (Tor), eepsite (i2p) and IPFS/IPNS.

### Goal: 
Create HTML + CSS static website generator for Zcash that could be run by anyone. Possible to split collector and publisher (if publisher attacked, can be created a new instance on different VPS). And, **do not touch the code at all**, let model
figure out.

### Tools: 
- Java 25, Quarkus, Tor, i2p, IPFS (Kubo), Docker, 
- Claude Code (Opus 4.7/4.8, Fable 5, GLM-5.2), NullAway + ErrorProne

### Result
It worked quite OK,the biggest issue was to set up frictionless setup of the app. And since it has some sense of security, I believe I kinda failed on this - needs ping pong between Collector and Publisher to swap keys (if you have divided setup).
Another interesting episode was dealing with HTML design - Claude was quite terrible, while GLM 5.2 did a better initial job, but after installing HTML/CSS plugin into Claude - GLM did not stand a chance. 

Furhermore, I had to provide couple of PoC to Claude to 'persuade' it that some implementations are possible - e.g. multiline graph in Chart.css or integration of NullAway with JDK 25. It was capable to solve parsing Prometheus schema for Zebra node and with a few iterations also figure
out quite interesting desings (but yeah, that needed human in loop, obviously). I think it did quite a good job (result that we see).

## [Infochat](https://github.com/zcat-app/infochat)
First project I started, but finished as second (because it is way bigger than Zecsite). The idea was to create halfday news digest that would be accesible via text messaging app. And since everyone creates chatbot for Telegram, I decided to
integrate it into real privacy apps that I actually use - SimpleX & Signal. And that was an adventure.

So, the idea is to have Java app that collects data from internet and provides you news that you can access through you messenger (SimpleX/Signal). You can have it completly private (DM), or share with family/friends in Chat rooms/Groups (however it is not scaled for tens of users).
Posts are processed - scanned (for security - like injections), tagged (e.g. 'Java') - that users can filter followed news by tags (very experimental) and store in vector DB - for possible querying. Each post have unique ID so it can be fetch as whole for full detail.

There are 2 mods of interaction:
- slash commands
  - e.g. /summary - returns summary of news, /zcash - returns current price of Zcash, etc.
  - should have deterministic execution
- chat
  - tries to find answer in vector DB filled with news post, if not found -> chat with configured LLM
  - nondeterministic outcome - based on LLM
  - 
There are 2 (maight say 3) tiers of user:
- User
  - can access news, add new news sources, interact with chatbot with commands or chatting
- Group Admin
  - as User but admin of group, can manage group related actions
- Bot Admin
  - as above + can invite/ban users, approve/discard suspicious news post - e.g. with promp injections, etc.
  
### Goal: 
Create chatbot for Simplex & Signal with:
- news digest (from cpreconfigured sources) + option to add own (private per user),
- RAG for retrieving details about latest news,
- general chatbot (configured local LLM or remote service like Deepseek, Claude, etc.)
- use private messenger apps: SimpleX, Signal
- make as simple setup as possible (I guess I suck in it)

### Tools: 
- Java 25, Quarkus, SimpleX, Signal Cli, Ollama, Llama.cpp, Postgres, Docker
- Claude Opus 4.7/4.8/5, Fable 5 (mostly used Claude models), Kimi K3/2.6 (security review, spec review, implementation), GLM-5.2 (implementation when Opus 4.8 degradated before Opus 5 release), Mimo-v2.5 (doc/spec review), Deepseek-V4 (doc/spec review) 

### Result
The whole project might take 3 months. It was roller coster. Especially with Anthropic - degradating quality of output from models before relasing new models (Opus 4.7->4.8,4.8->5, Sonnet 5, Fable 5), then Fable drama that got 'cured' by Moonshots Kimi K3. 
But overall, Opus is/was very capable when it was not held back by Anthropic - figured out SimpleX flow (after first it completly guessed it and crashed when tried in reality), Fable 5 felt very strong with smaller stupid stuck (clearing payload with multiple '\'
by removing one after each other but done fres ticket lifecycle - with escalations - 'hey there is one more, lets remove one again and go for code review (to escalate it again)...', etc. but over all it was quite impressive). 

GLM was quite OK, but it struggled a bit when codebase got bigger and had to iterate on its decision with burning incredible amount of tokens (here I doubt how cheap it is due to amount of tokens it burned). Mimo and DeepSeek were OK for document/spec review, but for coding or security review, it was rather weak.
They either had many false positive or completly missed HIGH or CRITICAL findings made by other models.

Overall it creates product that seems working, and is in the test drive. I will report on it more after some time of using it
