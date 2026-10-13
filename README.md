[index.html](https://github.com/user-attachments/files/33405994/index.html)
# world-online-hockey-recovery<!doctype html>
<html lang="az">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>World Online Hockey — Hesabın bərpası</title>
  <style>
    :root { color-scheme: dark; }
    * { box-sizing: border-box; }
    body { margin: 0; min-height: 100vh; display: grid; place-items: center; padding: 20px; background: radial-gradient(circle at top, #12345a, #06101f 65%); color: #eef8ff; font-family: system-ui, Arial, sans-serif; }
    main { width: min(100%, 470px); padding: 28px; border: 1px solid #35cfff; border-radius: 20px; background: rgba(6, 24, 43, .96); box-shadow: 0 16px 50px #0008; }
    h1 { margin: 0 0 10px; text-align: center; font-size: clamp(24px, 5vw, 32px); color: #71e6ff; }
    p { line-height: 1.55; color: #d2e4f4; }
    label { display: block; margin: 16px 0 7px; font-weight: 650; }
    input { width: 100%; padding: 13px 14px; border: 1px solid #47617c; border-radius: 10px; background: #0b1930; color: white; font-size: 16px; }
    button { width: 100%; margin-top: 18px; padding: 13px; border: 0; border-radius: 10px; background: #35cfff; color: #041425; font-size: 16px; font-weight: 750; cursor: pointer; }
    button:disabled { opacity: .55; cursor: wait; }
    #status { min-height: 28px; margin-top: 14px; overflow-wrap: anywhere; }
    .small { font-size: 13px; color: #a9bfd3; }
  </style>
</head>
<body>
  <main>
    <h1>Hesabın bərpası</h1>
    <p>Emaildəki təhlükəsiz keçiddən bura gəlmisənsə, yeni şifrəni təyin et. Əvvəlki qızılların, karyeran və nailiyyətlərin hesabında qalacaq.</p>
    <form id="reset-form">
      <label for="password">Yeni şifrə</label>
      <input id="password" type="password" minlength="8" autocomplete="new-password" required placeholder="Ən azı 8 simvol">
      <label for="confirm-password">Yeni şifrəni təkrar yaz</label>
      <input id="confirm-password" type="password" minlength="8" autocomplete="new-password" required placeholder="Şifrəni təkrar daxil et">
      <button id="submit" type="submit">Şifrəni yenilə</button>
    </form>
    <p id="status" role="status" aria-live="polite"></p>
    <p class="small">Bu səhifədə yalnız Supabase publishable açarı istifadə olunur. Heç vaxt service_role/secret açarı brauzerə yerləşdirmə.</p>
  </main>
  <script type="module">
    import { createClient } from 'https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/+esm';

    const SUPABASE_URL = 'https://gxfbhietulrngjtrkjkn.supabase.co';
    const SUPABASE_PUBLISHABLE_KEY = 'sb_publishable_cRK49ATJ376JfgdPvXhgEA_xeAbvkQp';
    const supabase = createClient(SUPABASE_URL, SUPABASE_PUBLISHABLE_KEY, {
      auth: { persistSession: true, autoRefreshToken: true, detectSessionInUrl: true }
    });

    const form = document.querySelector('#reset-form');
    const passwordInput = document.querySelector('#password');
    const confirmInput = document.querySelector('#confirm-password');
    const submitButton = document.querySelector('#submit');
    const status = document.querySelector('#status');

    // Allow Supabase JS to process the recovery tokens from the URL first.
    await new Promise(resolve => setTimeout(resolve, 350));
    const { data: { session }, error: sessionError } = await supabase.auth.getSession();
    if (sessionError || !session) {
      status.textContent = 'Bərpa sessiyası tapılmadı və ya keçidin vaxtı bitib. Oyuna qayıdıb yenidən bərpa emaili istə.';
      submitButton.disabled = true;
    } else {
      status.textContent = 'Yeni şifrəni daxil edə bilərsən.';
    }

    form.addEventListener('submit', async (event) => {
      event.preventDefault();
      const password = passwordInput.value;
      if (password.length < 8) {
        status.textContent = 'Şifrə ən azı 8 simvol olmalıdır.';
        return;
      }
      if (password !== confirmInput.value) {
        status.textContent = 'Şifrələr eyni deyil.';
        return;
      }
      submitButton.disabled = true;
      submitButton.textContent = 'YENİLƏNİR...';
      const { error } = await supabase.auth.updateUser({ password });
      if (error) {
        status.textContent = 'Şifrə yenilənmədi: ' + error.message + '. Keçidin vaxtı bitibsə, yenisini istə.';
        submitButton.disabled = false;
        submitButton.textContent = 'Şifrəni yenilə';
        return;
      }
      status.textContent = 'Şifrə uğurla yeniləndi! İndi World Online Hockey oyununa qayıdıb yeni şifrə ilə daxil ol.';
      form.reset();
      await supabase.auth.signOut();
      submitButton.textContent = 'UĞURLA TAMAMLANDI';
      // Remove tokens from the visible address bar after successful reset.
      history.replaceState(null, '', location.pathname + location.search);
    });
  </script>
</body>
</html>
