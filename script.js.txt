// Lee parámetros de la URL: ?nombre=&pases=&codigo=
const params = new URLSearchParams(window.location.search);
const nombre = (params.get("nombre") || "Invitado/a").trim();
const pases  = parseInt(params.get("pases") || "1", 10);
const codigo = (params.get("codigo") || "").trim();

// Asigna valores en pantalla (textContent evita HTML no deseado)
const guestNameEl = document.getElementById("guestName");
const passesCountEl = document.getElementById("passesCount");
const codeEl = document.getElementById("guestCode");
const guestNameBadgeEl = document.getElementById("guestNameBadge");
const passesBadgeEl = document.getElementById("passesBadge");

guestNameEl.textContent = nombre;
passesCountEl.textContent = Number.isFinite(pases) && pases > 0 ? pases : 1;
codeEl.textContent = codigo || "—";
guestNameBadgeEl.textContent = nombre;
passesBadgeEl.textContent = passesCountEl.textContent;

// Genera un enlace de WhatsApp para confirmar asistencia (ajusta tu número)
const phone = "5215555555555"; // Reemplaza por tu número con lada (52 = MX)
const msg = `Hola, soy ${nombre}. Confirmo mi asistencia (${passesCountEl.textContent} pase(s)). Mi código es ${codigo || "—"}.`;
const whLink = document.getElementById("whLink");
whLink.href = `https://wa.me/${phone}?text=${encodeURIComponent(msg)}`;
whLink.textContent = "Confirmar por WhatsApp";

// Construye el payload para el QR (usa el mismo link para validar en acceso)
const currentUrl = new URL(window.location.href);
// Opcional: puedes incluir timestamp para control básico
const payload = {
  t: Date.now(),
  url: currentUrl.toString(),
  nombre: nombre,
  pases: Number(passesCountEl.textContent),
  codigo: codigo || null,
};

// Genera QR en el div #qrcode
function makeQR(text){
  const container = document.getElementById("qrcode");
  container.innerHTML = "";
  /* global QRCode */
  new QRCode(container, {
    text,
    width: 180,
    height: 180,
    correctLevel: QRCode.CorrectLevel.M
  });
}
makeQR(JSON.stringify(payload));

/* ⚠️ Nota de seguridad:
   Este enfoque es "estático": los parámetros viajan en la URL y pueden ser modificados por el usuario.
   Para control de acceso estricto, necesitarías un backend (por ejemplo, Apps Script/SheetDB/Make) que valide un token.
*/