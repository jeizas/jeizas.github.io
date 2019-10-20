<!-- gitalk -->
<div id="gitalk-container"></div>
<link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
<script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>

   <script type="text/javascript">
        var gitalk = new Gitalk({
          clientID: '07829ee4fb4f0a6aa8c0',
          clientSecret: '6e7b3dc7519350b4c9a5f2f7951eebd6a9775b68',
          repo: 'jeizas.github.io',
          owner: 'jeizas',
          admin: ['jeizas'],
          id: '{{ page.title }}',
          distractionFreeMode: true
        })
        gitalk.render('gitalk-container')           
    </script>

