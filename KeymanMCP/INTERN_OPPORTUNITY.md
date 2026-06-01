# KeymanMCP — Intern Opportunity

## The Big Picture

You'll be building an **AI-powered tool that will help people create keyboards for languages around the world**.

**Keyman** ([keyman.com](https://keyman.com)) is **the world's largest keyboard engine for lesser-resourced languages**. It powers keyboards for over 1,000 languages — including minority, endangered, and underserved languages that have zero commercial keyboard support. From Amharic to Zulu, from Khmer to Quechua, Keyman is how linguists, missionaries, language activists, and indigenous communities get professional keyboard technology.

Right now, creating a Keyman keyboard requires coordinating 6+ interdependent files inside Keyman Developer — a process that's error-prone, time-consuming, and inaccessible to non-technical users. **KeymanMCP will change that by letting AI assistants (Claude, ChatGPT, etc.) do the heavy lifting.**

---

## What You'll Build

**KeymanMCP** will be a Python MCP (Model Context Protocol) server that teaches AI assistants how to create and adapt Keyman keyboards. Think of it as giving Claude the technical knowledge to:

- **Create from scratch:** "I need a keyboard for Amharic" → AI generates all the code files automatically
- **Adapt existing:** "Make the Khmer Angkor keyboard work for a different language" → AI clones, renames, and updates all internal references without introducing bugs
- **Validate & build:** "Check if my keyboard is correct" → AI validates consistency across all files and compiles it

---

## Impact: Why This Matters

### For Language Communities
- **Over 1,000 languages currently rely on Keyman keyboards** ([keyman.com/keyboards](https://keyman.com/keyboards)). Your tool will enable faster, better keyboard development for all of them.
- Keyboard developers will no longer need weeks of training to understand file formats
- Languages with smaller speaker populations will finally be able to get professional keyboards without external dependency
- AI assistants will become accessible tools for language documentation and technology
- Indigenous language advocates, missionary organizations, and academic linguists will benefit directly

### For You (The Team)
- You'll be building **real infrastructure** that will be used by linguists, missionaries, language activists, and endangered language communities worldwide
- Your code will directly enable **AI-assisted development** — a growing trend in language technology
- You'll learn patterns that apply to any API documentation system (medical records, financial APIs, scientific databases)
- **Your work will be visible and credited** — potentially integrated into the official Keyman platform

### For AI + Coding
- This will be a **concrete example of teaching AI to code correctly**
- You'll build an "API index" — essentially a knowledge base that will keep AI from hallucinating
- The pattern you create (extract specs → build index → let AI generate code) will be applicable far beyond Keyman
- You'll understand how to make **complex domain knowledge accessible to machines**

---

## What You'll Learn

### Technical Skills
- **Python** — async programming, file I/O, data structures, regex
- **API design** — how to document systems so machines (and humans) can understand them
- **Testing & validation** — catching edge cases and inconsistencies
- **File format parsing** — XML, JSON, YAML, and how to validate data
- **Collaborative development** — working in a 4-person team with code review

### Real-World Development
- How to coordinate interdependent systems (the keyboard files are like microservices that must agree)
- How to extract knowledge from source code and documentation automatically
- How to design for both human users and machine consumers (AI assistants)
- Error messages that actually help people fix problems

### Domain Knowledge
- How keyboards work (more interesting than you'd think!)
- Language technology and internationalization
- How AI assistants think about problems (via building the tools they use)

---

## The Team & Mentorship

**You won't be alone.** The team is:
- **1 AI/Keyman Specialist** — Your technical mentor, expert in both AI and Keyman internals. Pair-programs with you on tricky parts, reviews code, unblocks architectural questions.
- **1 Keyman Trainer** — Domain expert who tests your work, explains what users actually need, ensures your tool is useful in the real world.
- **3 Interns (you + 2 others)** — Each focused on a different piece: API extraction, file parsing, and server implementation. Daily standups, shared responsibility for testing.

**You'll code throughout the summer with daily feedback** — not a "disappear and check in at the end" internship.

---

## What You'll Work On (Together)

The three of you will work as a **collaborative team**, not in siloed roles. You'll pair-program, code review each other's work, and help each other ship.

### The Core Work (All Interns)

**Foundations**
- Together with the AI Specialist, you'll build the API index extraction framework
- Implement parsers for all keyboard file types
- Get the MCP server running and calling the first tool (`build_keyboard`)
- Establish the core: `python src/server.py` will work

**Implementation Sprint**
- You'll divide work by tool (`generate_keyboard`, `copy_keyboard`, `validate_consistency`, `inspect_keyboard`)
- But you won't be locked in — if someone finishes early, they'll help with testing or documentation
- Daily code reviews with each other (and the AI Specialist)
- You'll discover edge cases, file issues, and fix them together

**Integration & Testing**
- All of you will run end-to-end tests together
- You'll work with the Keyman Trainer to validate the tool works for real users
- You'll write documentation, fix bugs, optimize performance
- Ship a production-ready server that's ready for the Keyman community

### Skills You'll Gain

- **Python:** Async programming, file I/O, data structures, subprocess management, testing
- **API design:** Building systems that both humans and AI can use
- **File format parsing:** XML, JSON, regex, validation
- **Error handling:** Making code robust and user-friendly
- **Teamwork:** Pair-programming, code review, daily standups, shared ownership
- **Testing:** Catching edge cases, writing tests that matter
- **Real-world development:** Shipping code that people use

## What Success Looks Like

**By the end of the summer, you'll have:**

✅ An MCP server that AI assistants can actually use
✅ A tool that will create valid Keyman keyboards from scratch in <5 seconds
✅ A tool that will safely adapt existing keyboards in <2 seconds
✅ Full validation that will catch consistency errors early
✅ An API index with 256+ key names, 30+ system stores, 120+ error codes, and code patterns
✅ Production-ready code with tests, documentation, and error handling
✅ A portfolio piece that demonstrates AI + coding expertise

**You'll present this to the Keyman community and potentially have it integrated into their official tools.**

---

## Why Now?

- **AI is changing how code gets written.** This project shows the pattern: index your API → let AI generate code → provide feedback → improve the index.
- **Keyboard development is underserved.** Keyman has millions of users but no AI-assisted workflows yet. You'll be first.
- **This is an intensive summer project.** Not a semester-long commitment — you'll see tangible results quickly and ship real software.
- **The team is ready to ship.** We've already designed the architecture. You're implementing a solid plan, not figuring out requirements.

---

## Tech Stack (No Surprises)

- **Python 3** — Simple, readable, widely-used
- **MCP (Model Context Protocol)** — How AI assistants like Claude talk to tools
- **Keyman `kmc` CLI** — You call it as a subprocess, not reinvent it
- **Standard libraries:** `subprocess`, `json`, `xml.etree`, `regex`, `pathlib`
- **Third-party:** `mcp`, `pydantic`, `lxml` (6 packages total, all lightweight)

---

## Questions?

**"Is this really just an internship?"**
Yes, but it ships real code. You'll see your work in production by summer's end.

**"What if I don't know Keyman?"**
That's fine. The team leads will teach you. Your job is Python, parsing, and AI integration — not Keyman expertise.

**"Will I be pair-programmed the whole time?"**
No, you'll own your piece. But the AI Specialist will pair with you on tricky parts, and you'll do code review with the team daily.

**"What if I make a mistake?"**
That's how learning works. Tests catch bugs. The team reviews code. You iterate and ship fixes — just like real development.

**"Can I work on this remotely?"**
We will start with an in-person week, then you will probably work remotely. Daily standups are online. You'll need D:\Github\keyman and D:\Github\keyboards cloned locally, but that's it.

---

## Apply If You:

- 🐍 Know Python (or want to learn it intensively)
- 🧠 Enjoy solving puzzles (parsing, validation, edge cases)
- 📝 Can explain your thinking clearly (code review, standups)
- 🌍 Care about language technology and accessibility
- 🚀 Want to ship real software this summer, not hypothetical projects

---

## Next Steps

1. **Read** `EXECUTIVE_SUMMARY.md` for a 2-minute overview
2. **Read** `PLAN.md` for technical details
3. **Talk to us** about which role interests you most
4. **Meet the team** — 30-min call with the AI Specialist and Keyman Trainer
5. **Start building** — We'll pair-program with you on the foundational pieces

---

## One More Thing

This project exists because **language communities deserve better tools.**

**Keyman's impact:** Over 1 million people worldwide use Keyman keyboards daily. Teachers use them in classrooms. Linguists use them for language documentation. Missionaries use them in community outreach. Indigenous language advocates use them to preserve endangered languages.

**Your impact:** When you ship KeymanMCP, you'll be enabling:
- A linguist in Papua New Guinea to create a keyboard for a language with 500 speakers
- A mission organization to deploy technology for underserved communities
- A university researcher to document an endangered language digitally
- An indigenous language educator to teach reading and writing in their heritage language

Your code will help people **write in their languages.** That's not nothing.

---

## Learn More

- **Keyman Official Site:** [keyman.com](https://keyman.com) — Overview, keyboard downloads, documentation
- **Keyman Keyboards:** [keyman.com/keyboards](https://keyman.com/keyboards) — 1000+ keyboards by language
- **Keyman GitHub:** [github.com/keymanapp/keyman](https://github.com/keymanapp/keyman) — Where the developer tools live (we're building an MCP server for this!)
- **About SIL (creators of Keyman):** [sil.org](https://sil.org) — Language technology for global impact

---

**Questions? Contact:**
- **AI Specialist (Tech Lead):** Matthew Lee (Matthew_Lee@sil.org)
- **Keyman Trainer (Product Lead):** Doug Higby (doug_higby@sil.org)
