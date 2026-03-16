# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the hints were backwards").

The UI looked fine at first, but the hints were immediately wrong: guessing too high told me to go higher, and guessing too low told me to go lower. The comparison operators in check_guess are just flipped. The second bug was sneakier: on every even attempt, the code casts secret to a string, so the comparison becomes lexicographic. "9" > "10" as strings, so the feedback silently breaks without any error.
---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

I used Claude throughout. It correctly identified the flipped hint messages in check_guess, which I verified by tracing the logic manually: if guess > secret, you've guessed too high, so the message should say go lower. Where it misled me: it described the string comparison bug as something that would "cause a type error," so I expected a crash. It actually fails silently with wrong output, which I only caught by running it with a specific edge case (guessing 9 when secret was 10).

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

I fixed a bug only when I could construct the exact input that broke the old code and confirm the new code handled it correctly. For the string comparison issue, I tested check_guess(9, "10") vs check_guess(9, 10) directly and confirmed they matched after the fix. Before the fix, the string version returned "Too High" because "9" > "10" lexicographically. Claude helped me think through which inputs would expose the divergence.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

Every user interaction causes Streamlit to rerun the entire script, which means regular variables reset every time. st.session_state is a persistent dictionary that survives reruns, so anything that needs to carry over (like the secret number or attempt count) has to live there. The tricky part is making sure state is initialized correctly and not accidentally overwritten on each rerun.
---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.

The habit I want to keep: verify a fix by testing the exact failure case, not just checking that things "seem to work." What I'd do differently: ask AI to describe the observable symptom of a bug, not just the cause, since "silent wrong output" and "crash" lead you to look in completely different places. This project made me more skeptical of AI-generated code in a useful way; the bugs here were subtle and plausible-looking, which is harder to catch than anything that fails loudly.