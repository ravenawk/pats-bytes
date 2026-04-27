---
title: "Get in Touch"
description: "I'd love to hear from you!"
url: "/contact/"
---

<div id="contact-intro">I'd love to hear from you! Whether you spot an error in a post, have a question about something I've written, or just want to share your own homelab adventures — drop me a message.</div>

<style>
.contact-honeypot { position: absolute; left: -9999px; opacity: 0; height: 0; overflow: hidden; pointer-events: none; }
.contact-form { display: flex; flex-direction: column; gap: 1.25rem; margin: 2rem 0 1.5rem; max-width: 680px; }
.contact-field { display: flex; flex-direction: column; gap: 0.4rem; }
.contact-field label { font-size: 0.9rem; font-weight: 600; color: var(--primary); }
.contact-form input[type="text"],
.contact-form input[type="email"],
.contact-form select,
.contact-form textarea {
  width: 100%; padding: 0.55rem 0.75rem; border: 1px solid var(--border);
  border-radius: 6px; background: var(--entry); color: var(--primary);
  font-size: 0.95rem; font-family: inherit; box-sizing: border-box;
  transition: border-color 0.15s ease, box-shadow 0.15s ease;
  -webkit-appearance: none; appearance: none;
}
.contact-form select {
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='8' viewBox='0 0 12 8'%3E%3Cpath fill='%23888' d='M1 1l5 5 5-5'/%3E%3C/svg%3E");
  background-repeat: no-repeat; background-position: right 0.75rem center; padding-right: 2rem; cursor: pointer;
}
.contact-form textarea { resize: vertical; min-height: 140px; }
.contact-form input:focus, .contact-form select:focus, .contact-form textarea:focus {
  outline: none; border-color: #4a90d9;
  box-shadow: 0 0 0 3px rgba(74, 144, 217, 0.2);
}
.contact-submit {
  display: inline-block; padding: 0.6rem 1.75rem; background: #1a73e8;
  color: #fff; font-size: 1rem; font-weight: 600; font-family: inherit;
  border: none; border-radius: 6px; cursor: pointer;
  transition: background 0.15s ease, transform 0.1s ease;
}
.contact-submit:hover { background: #1558b0; }
.contact-submit:active { transform: scale(0.98); }
.contact-meta { margin-top: 1.75rem; padding-top: 1rem; border-top: 1px solid var(--border); font-size: 0.9rem; color: var(--secondary); }
.contact-meta p { margin: 0.4rem 0; }
.contact-meta strong { color: var(--primary); }
</style>

<form name="contact" method="POST" action="/contact/?success=true" data-netlify="true" netlify-honeypot="bot-field" class="contact-form">
  <input type="hidden" name="form-name" value="contact" />

  <p class="contact-honeypot" aria-hidden="true">
    <label>Don't fill this out if you're human: <input name="bot-field" tabindex="-1" autocomplete="off" /></label>
  </p>

  <div class="contact-field">
    <label for="contact-name">Name</label>
    <input id="contact-name" type="text" name="name" placeholder="Your name" required autocomplete="name" />
  </div>

  <div class="contact-field">
    <label for="contact-email">Email</label>
    <input id="contact-email" type="email" name="email" placeholder="you@example.com" required autocomplete="email" />
  </div>

  <div class="contact-field">
    <label for="contact-subject">Subject</label>
    <select id="contact-subject" name="subject">
      <option value="General question">General question</option>
      <option value="Post correction">Post correction</option>
      <option value="Homelab chat">Homelab chat</option>
      <option value="Other">Other</option>
    </select>
  </div>

  <div class="contact-field">
    <label for="contact-message">Message</label>
    <textarea id="contact-message" name="message" rows="7" placeholder="What's on your mind?" required></textarea>
  </div>

  <div data-netlify-recaptcha="true"></div>

  <div>
    <button type="submit" class="contact-submit">Send Message</button>
  </div>
</form>

<div id="contact-success" style="display:none;" role="alert">
  <p>✅ <strong>Message sent!</strong> 
</div>

<div class="contact-meta">
  <p><strong>Privacy note:</strong> <em>This form is handled by Netlify and only collects what you provide above. No tracking, no nonsense.</em></p>
</div>

<script>
if (window.location.search.includes('success=true')) {
  document.querySelector('.contact-form').style.display = 'none';
  document.getElementById('contact-intro').style.display = 'none';
  document.getElementById('contact-success').style.display = 'block';
}
</script>
