// ==UserScript==
// @name         GeoFS Basit HUD
// @namespace    geofs-hud
// @version      4.0
// @description  GeoFS'te hız, irtifa, rota ve dikey hızı gösteren küçük bir panel
// @match        https://www.geo-fs.com/geofs.php*
// @match        https://geo-fs.com/geofs.php*
// @grant        none
// ==/UserScript==

(function () {
  'use strict';

  // GeoFS yüklenene kadar bekle
  const bekle = setInterval(() => {
    if (window.geofs && geofs.animation && geofs.animation.values && geofs.aircraft && geofs.aircraft.instance) {
      clearInterval(bekle);
      baslat();
    }
  }, 1000);

  function baslat() {
    // Panel
    const panel = document.createElement('div');
    panel.style.cssText = [
      'position:fixed', 'top:12px', 'left:12px', 'z-index:99999',
      'background:rgba(0,0,0,0.6)', 'color:#7CFC00',
      'font:14px/1.5 monospace', 'padding:10px 14px',
      'border-radius:8px', 'pointer-events:none', 'min-width:170px'
    ].join(';');
    document.body.appendChild(panel);

    // H tuşu ile paneli aç/kapat
    let gorunur = true;
    document.addEventListener('keydown', (e) => {
      if (e.key.toLowerCase() === 'h' && !/INPUT|TEXTAREA/.test(document.activeElement.tagName)) {
        gorunur = !gorunur;
        panel.style.display = gorunur ? 'block' : 'none';
      }
    });

    setInterval(() => {
      if (!gorunur) return;
      const v = geofs.animation.values;
      const hiz = Math.round(v.kias || 0);               // knot (gösterge hızı)
      const irtifa = Math.round(v.altitude || 0);        // feet (MSL)
      const rota = Math.round(v.heading360 || 0);        // derece
      const dikey = Math.round(v.verticalSpeed || 0);    // ft/dk
      const gaz = Math.round((v.throttle || 0) * 100);   // yüzde

      panel.innerHTML =
        'HIZ&nbsp;&nbsp;&nbsp;: ' + hiz + ' kt<br>' +
        'ALTITUDE : ' + irtifa + ' ft<br>' +
        'ROTA&nbsp;&nbsp;: ' + String(rota).padStart(3, '0') + '°<br>' +
        'DİKEY&nbsp;&nbsp;: ' + dikey + ' ft/dk<br>' +
        'GAZ&nbsp;&nbsp;&nbsp;&nbsp;: %' + gaz;
    }, 200);
  }
})();
