# Login-authentication-system
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Demo Auth — Login</title>
  <style>
    body{font-family:system-ui,Segoe UI,Roboto,Helvetica,Arial;display:flex;min-height:100vh;align-items:center;justify-content:center;background:#f3f4f6}
    .card{background:white;padding:24px;border-radius:12px;box-shadow:0 6px 18px rgba(0,0,0,0.08);width:360px}
    input{width:100%;padding:10px;margin:8px 0;border:1px solid #d1d5db;border-radius:6px}
    button{width:100%;padding:10px;border-radius:8px;border:0;background:#374151;color:white;font-weight:600}
    .muted{color:#6b7280;font-size:13px;margin-top:8px;text-align:center}
    .switch{margin-top:12px;text-align:center}
    .error{color:#b91c1c;font-size:13px}
  </style>
</head>
<body>
  <div class="card">
    <h2 id="title">Login</h2>

  <form id="form">
      <input type="email" id="email" placeholder="Email" required />
      <input type="password" id="password" placeholder="Password" minlength="6" required />
      <div id="error" class="error" role="alert" aria-live="polite"></div>
      <button id="submit">Sign in</button>
    </form>

   <div class="switch">
      <button id="toggle" style="background:transparent;color:#374151;border:0;cursor:pointer">Don't have an account? Register</button>
    </div>

   <p class="muted">This demo stores users in the server's memory and returns a JWT. Not for production.</p>
  </div>

  <script>
    const form = document.getElementById('form');
    const toggleBtn = document.getElementById('toggle');
    const title = document.getElementById('title');
    const submitBtn = document.getElementById('submit');
    const errorEl = document.getElementById('error');

    let mode = 'login'; // or 'register'

    toggleBtn.addEventListener('click', (e) => {
      e.preventDefault();
      mode = mode === 'login' ? 'register' : 'login';
      title.textContent = mode === 'login' ? 'Login' : 'Register';
      submitBtn.textContent = mode === 'login' ? 'Sign in' : 'Create account';
      toggleBtn.textContent = mode === 'login' ? "Don't have an account? Register" : 'Have an account? Login';
      errorEl.textContent = '';
    });

    form.addEventListener('submit', async (e) => {
      e.preventDefault();
      errorEl.textContent = '';

      const email = document.getElementById('email').value.trim().toLowerCase();
      const password = document.getElementById('password').value;

      // Simple client-side checks
      if (!email || !password) { errorEl.textContent = 'Fill in both fields.'; return }
      if (password.length < 6) { errorEl.textContent = 'Password must be at least 6 characters.'; return }

      submitBtn.disabled = true;
      submitBtn.textContent = mode === 'login' ? 'Signing in...' : 'Creating...';

      try {
        const endpoint = mode === 'login' ? '/api/login' : '/api/register';
        const res = await fetch(endpoint, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ email, password })
        });
        const data = await res.json();

        if (!res.ok) throw new Error(data.message || 'Request failed');

        // For demo we store JWT in localStorage. In production prefer HttpOnly Secure cookies.
        localStorage.setItem('jwt', data.token);

        // Redirect to protected page or show success
        alert(mode === 'login' ? 'Login successful!' : 'Account created! You are logged in.');
        // window.location.href = '/dashboard.html';
      } catch (err) {
        errorEl.textContent = err.message;
      } finally {
        submitBtn.disabled = false;
        submitBtn.textContent = mode === 'login' ? 'Sign in' : 'Create account';
      }
    });
  </script>
</body>
</html>
