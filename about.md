---
layout: article
permalink: /about/
---

<style>
  body {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    margin: 0;
    padding: 2rem;
    background: #f7f7f7;
  }

  .about-container {
    max-width: 1200px;
    margin: 0 auto;
  }

  .about-title {
    text-align: center;
    margin-bottom: 2rem;
  }

  .team-grid {
    display: grid;
    grid-template-columns: repeat(5, minmax(0, 1fr));
    gap: 0.5rem;
  }

  .member-card {
    background: #fff;
    border-radius: 12px;
    padding: 1.5rem 1rem;
    text-align: center;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);

    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .member-photo {
    width: 96px;
    height: 96px;
    border-radius: 50%;
    object-fit: cover;
    margin-bottom: 0.75rem;
  }

  .member-name {
    font-weight: 600;
    margin-bottom: 0.5rem;
  }

    .member-links {
    /* NEW: push links to bottom */
    margin-top: auto;
  }

  .member-links a {
    display: inline-block;
    margin: 0 0.25rem;
    font-size: 0.9rem;
    color: #0366d6;
    text-decoration: none;
  }

  .member-links a:hover {
    text-decoration: underline;
  }
</style>

<main class="about-container">
  <h1 class="about-title">About the TalentedSoup Team</h1>

  <section class="team-grid">
    <!-- Member 1 -->
    <article class="member-card">
      <img
        src="{{ '/assets/images/commander_face.svg' | relative_url }}"
        alt="Photo of Member 1"
        class="member-photo"
      />
      <div class="member-name">Shen Chen        </div>
      <div class="member-links">
        <a href="https://linkedin.com/in/member1" target="_blank" rel="noopener">LinkedIn</a>
        <a href="https://github.com/tonichencs" target="_blank" rel="noopener">GitHub</a>
      </div>
    </article>

    <!-- Member 2 -->
    <article class="member-card">
      <img
        src="{{ '/assets/images/architect_face.svg' | relative_url }}"
        alt="Photo of Member 2"
        class="member-photo"
      />
      <div class="member-name">Nur Ertug        </div>
      <div class="member-links">
        <a href="https://linkedin.com/in/member2" target="_blank" rel="noopener">LinkedIn</a>
        <a href="https://github.com/NurErtugn" target="_blank" rel="noopener">GitHub</a>
      </div>
    </article>

    <!-- Member 3 -->
    <article class="member-card">
      <img
        src="{{ '/assets/images/commander_face.svg' | relative_url }}"
        alt="Photo of Member 3"
        class="member-photo"
      />
      <div class="member-name">Sebastian Maier      </div>
      <div class="member-links">
        <a href="https://linkedin.com/in/member3" target="_blank" rel="noopener">LinkedIn</a>
        <a href="https://github.com/SebastianMaierEPFL" target="_blank" rel="noopener">GitHub</a>
      </div>
    </article>

    <!-- Member 4 -->
    <article class="member-card">
      <img
        src="{{ '/assets/images/debater_face.svg' | relative_url }}"
        alt="Photo of Member 4"
        class="member-photo"
      />
      <div class="member-name">George-Stelian Petran</div>
      <div class="member-links">
        <a href="https://linkedin.com/in/member4" target="_blank" rel="noopener">LinkedIn</a>
        <a href="https://github.com/GeorgePetran" target="_blank" rel="noopener">GitHub</a>
      </div>
    </article>

    <!-- Member 5 -->
    <article class="member-card">
      <img
        src="{{ '/assets/images/architect_face.svg' | relative_url }}"
        alt="Photo of Member 5"
        class="member-photo"
      />
      <div class="member-name">Magdalena Turcanu</div>
      <div class="member-links">
        <a href="https://linkedin.com/in/member5" target="_blank" rel="noopener">LinkedIn</a>
        <a href="https://github.com/magdaturcanu1" target="_blank" rel="noopener">GitHub</a>
      </div>
    </article>
  </section>
</main>