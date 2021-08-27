---
title: Metrical Positional Encoding

midi_files:
  - 00049.mid|<img src="https://i.giphy.com/media/Ju7l5y9osyymQ/giphy.webp" width="150px">
  - 00353.mid|Stayin' Alive (Bee Gees)
  - 00253.mid|Spain (Chick Corea)
  - 00037.mid|Different Drum (The Stone Poneys)
  - 00109.mid|Since I've Been Loving You (Led Zeppelin)
  - 00152.mid|Nowhere Man (The Beatles)
  - 00220.mid|Let's Get Physical (Olivia Newton-John)
  - 00249.mid|Dirty Love (Frank Zappa)
  - 00242.mid|Hotel California (Eagles)
  - 00254.mid|A Whiter Shade Of Pale (Procol Harum)
  - 00129.mid
  - 00355.mid
  - 00038.mid
  - 00091.mid
  - 00057.mid
  - 00162.mid
  - 00010.mid
  - 00020.mid
  - 00033.mid
  - 00043.mid
  - 00047.mid
  - 00166.mid
  - 00340.mid
  - 00358.mid
---

## Examples

{% for item in page.midi_files %}
{% assign f = item | split: "|" | first %}
{% assign name = item | split: "|" | last %}
### {{ name | default: f }}
<div class="tabbed-midi-player">
<div class="tabs">
  {% comment %}<a href="#" data-midi-url="midi/prompt/{{ f }}" class="selected">Prompt</a>{% endcomment %}
  <a href="#" data-midi-url="midi/lin_trans_tt_mpe_v01/{{ f }}">&Delta; + MPE</a>
  <a href="#" data-midi-url="midi/lin_trans_tt_nope_v01/{{ f }}">&Delta; + noPE</a>
  <a href="#" data-midi-url="midi/lin_trans_tt_v01/{{ f }}">&Delta; + APE</a>
  <a href="#" data-midi-url="midi/lin_trans_tt-bre_mpe_v01/{{ f }}">BR + MPE</a>
  <a href="#" data-midi-url="midi/lin_trans_tt-bre_v01/{{ f }}">BR + APE</a>
</div>
<midi-visualizer></midi-visualizer>
<midi-player sound-font></midi-player>
</div>
{% endfor %}


<script>
BASE_URL = '{{ "/" | relative_url }}';

document.querySelectorAll('midi-visualizer').forEach(function (visualizer) {
  visualizer.config.noteHeight = 4;
  visualizer.config.pixelsPerTimeStep = 30;
  visualizer.classList.add('loading');
});

document.querySelectorAll('.tabbed-midi-player').forEach(function (container) {
  // Make first tab selected
  if (!container.querySelector('a[data-midi-url].selected')) {
    container.querySelector('a[data-midi-url]').classList.add('selected');
  }

  const visualizer = container.querySelector('midi-visualizer');
  const player = container.querySelector('midi-player');
  const defaultUrl = BASE_URL + container.querySelector('a[data-midi-url].selected').dataset.midiUrl;
  player.addVisualizer(visualizer);
  player.src = defaultUrl;
  visualizer.src = defaultUrl;

  player.addEventListener('load', function() {
    visualizer.classList.remove('loading');
    visualizer.querySelector('.piano-roll-visualizer').scrollLeft = 0;
  });
  player.addEventListener('start', function () {
    visualizer.querySelector('.piano-roll-visualizer').scrollLeft = 0;
  });

  // Tabs
  container.querySelectorAll('a[data-midi-url]').forEach(function (link) {
    link.addEventListener('click', function (event) {
      event.preventDefault();
      player.src = BASE_URL + link.dataset.midiUrl;
      visualizer.src = BASE_URL + link.dataset.midiUrl;
      visualizer.classList.add('loading');
      container.querySelector('a[data-midi-url].selected').classList.remove('selected');
      link.classList.add('selected');
    });
  });
});

document.querySelector('#popExtrapolate').addEventListener('change', function () {
  document.querySelectorAll('#pop-piano a[data-midi-url]').forEach(function (tab) {
    if (document.querySelector('#popExtrapolate').checked) {
      if (tab.dataset.midiUrl.indexOf('_extrapolation') < 0) {
        tab.dataset.midiUrl = tab.dataset.midiUrl.replace('.mid', '_extrapolation.mid');
      }
    } else {
      tab.dataset.midiUrl = tab.dataset.midiUrl.replace('_extrapolation', '');
    }

    if (tab.classList.contains('selected')) {
      tab.click();
    }
  });
});
</script>
