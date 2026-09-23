# Android Interview Handbook

A community-friendly, topic-wise collection of Android interview questions, answers, follow-ups, production scenarios, and system-design problems.

The goal is **understanding, not memorization**. Questions are organized from Basic → Intermediate → Advanced → Senior/Lead, with an emphasis on the reasoning expected from experienced Android developers.

## Current content

### Data Storage — complete
A curated set of topic-wise interview material covering:

- Storage fundamentals
- SharedPreferences
- DataStore
- Room
- Room relationships and query performance
- Transactions and consistency
- Database migrations
- Scoped Storage
- MediaStore and Storage Access Framework
- Files and caching
- Storage security
- Offline-first architecture
- Senior/Lead system design

Start here:

- [Data Storage](./data-storage/)

## Repository structure

```text
android-interview-handbook/
├── README.md
├── CONTRIBUTING.md
├── LICENSE
├── ROADMAP.md
├── TOPICS.md
│
└── data-storage/
    ├── README.md
    ├── fundamentals.md
    ├── shared-preferences.md
    ├── datastore.md
    ├── room.md
    ├── room-relations.md
    ├── room-transactions.md
    ├── room-migrations.md
    ├── scoped-storage.md
    ├── mediastore-and-saf.md
    ├── file-storage-and-caching.md
    ├── storage-security.md
    ├── offline-first.md
    └── system-design.md
```

## Difficulty

- 🟢 Basic — fundamentals
- 🟡 Intermediate — implementation and design
- 🔴 Advanced — production reasoning
- 🟣 System Design — architecture and trade-offs

## Philosophy

For every technology, ask:

1. What problem does it solve?
2. When should I use it?
3. When should I not use it?
4. What are the trade-offs?
5. What happens when things fail?
6. How does it behave at scale?
7. How would I test it?
8. How would I migrate away from it?

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md).

## License

MIT. See [LICENSE](./LICENSE).


### Interview revision workflow

Each question includes a **Sample Senior Answer**, a question-specific Senior/Lead reasoning section, and a **Quick Revision** summary. Common follow-ups are intentionally presented as prompts first: where a follow-up maps to another handbook question, it links there so you can reason through it before opening the answer; otherwise the answer is hidden in a collapsible section.


## Interview Practice Approach

This repository is designed as an **answer key**, not a collection of artificial “senior answers”. Start with the basic question, answer it aloud, then use progressively deeper follow-ups to probe mechanism, edge cases, trade-offs, and real-world usage. Related follow-ups can point to other questions, and hidden answers can be revealed only after you have reasoned through them.

Where theory alone is difficult to visualize, topics include small Kotlin/Android examples.
