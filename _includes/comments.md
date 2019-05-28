<!-- gitalk -->
<div id="gitalk-container"></div>
<link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
<script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>

   <script type="text/javascript">
        var gitalk = new Gitalk({
          clientID: 'ea42f33cc56ca3882c6b',
          clientSecret: '0342404b766f80d2c6e675235d44cf22538fe4e5',
          repo: 'jeizas.github.io',
          owner: 'jeizas',
          admin: ['jeizas'],
          id: '{{ page.title }}',
          distractionFreeMode: true
        })
        gitalk.render('gitalk-container')           
    </script>

