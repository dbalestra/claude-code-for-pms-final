# 04 · X-Ray Vision — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. Last session the numbers showed you what happened: a
ping used to wait ninety seconds and now it waits sixty, people
missed pings they used to catch, and missing one counts the same as
turning one down — so four responders stopped hearing from us
altogether.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.
What is different now vs before the 4.2 release?

### 2.
Who decided to change those weights and why?

### 3.
lets check the handover doc for more info

### 4.
Can you create a visual diagram of the code to help better understand the sequencing?

### 5.
Why does a missed ping count the same as a decline?

### 6.
did the change to who gets asked first apply to responders who'd already been turning jobs down, or just new ones? Can't tell from the code and don't want to guess on this. Anyone know?

### 7.
here is the working hypothesis: Some responders are getting no pings at all is due to their low rating, which is a result of a series of missed calls (due to shorter response windows) early after the Aug 12 change leading to their rating placing them well below other responders who show a regular rate of responding faster. The new system punished slow responders early on and gave them no chance to recover while also overloading the fast responders.

Read the pings table alongside the code to see if this rings true

### 8.
Who is the staff engineer?

### 9.
Were people's ratings reset with the 12 Aug release?

### 10.
Draft the questions for Wen Li and Ravi

### 11.
I do want to get to a direct answer to the question the manager posted

### 12.
What seems to be missing here?

### 13.
So for someone very low rated, what would be the steps they would need to take to get back up?

### 14.
What could we change to help avoid these problems in the future? Give me a plain english pitch and point at where the code would need to be adjusted

### 15.
Draft the questions for Ravi on all responders' scores, with a callout of the specific 4 at the bottom to be prominent
