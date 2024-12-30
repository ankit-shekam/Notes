[back to previous page](./HLD.md)

---

# Framework for HLD interviews

- Process(how you approach the problem) is much more crucial than the final design
- process is:
   - your design choices (why A over B)
   - Ability to collaborate, work under pressure, resolve ambiguity, asking good questions
   - Avoiding red flags - over-engineering/narrow-minded/stubborn

## 4 Step process : 
**Considering the entire interview usually is 45-60mins**



#### 1 - Understnad the problem -> (10mins)

- Giving out answers quickly without thinking about the problem and understanding it will not ge you bonus pts.

- **Clarify Requirements & Assumptions first**

   - if interviewer says **you assume** -> **Write down your assumptions** for later reference

   - Ask Questions to minimize the open-ended nature of the questions
      - What Specific feature we are trying to build
      - Number of Users we expect
      - Speed of scale up we expect in near future
      - Is There some specific tech stack restriction 
      - Can we use some pre-existing services to make the system simple


#### 2 - Propose high-level design and get buy-in -> (10-15mins)

- Come up with initial design and constantly seek feedbaack and approval from interviewer, treat them as team mates rather than interviewer

- Draw boxes for simplicity (go into detail later) - Client/API-server/CDN/Message-Queue/Cache...

- Do back of the envelope estimations

- Ask if we Should include API-endpoints & DB-schema here or discuss it later in details (this depends how Low-level the problem is, Mostly done LLD interview)


#### 3 - Design Deep Dive -> (25-30mins)

- By Now you have got approval on : 
   - Overall goals for immediate implementation
   - got a high level system blue-print
   - had initial discssion on areas of the system to focus on

- Now : Prioritize discussion on the system components that need to be looked into in more detail:
   - eg. URL-shortner -> deep dve into the hash function
   - eg. Chat-Application -> reduce latency && offline/online status indicator

- Take care of **TIME MANAGEMENT**
   - very easy to get lost in the detailed discussion and loose track of time, its about designing a system, not about your depth of knowledge


#### 4 - Wrap-up -> (5-10mins)

- Interviewer might ask some follow-up questions OR Ask you about some other thing you would have liked to discuss if gotten more time
   - no design is perfect, and there is always room for improvement
   - good opportunity to showcase critical thinking and openneds to constructive feedback even from your own self

- Operational aspects can be discussed - Logging/Monitoring, deployment...
- Further Scaling challanges once the userbase grows
- Failure cases - Network failure / Server failure are also interesting
- if none of the above -> You could go over the re-cap of the discussion so far