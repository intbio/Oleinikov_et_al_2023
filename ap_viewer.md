### Nucleosome complexes with proteins containing an arginine anchor.
[Back](https://intbio.github.io/Oleinikov_et_al_2023)

<html>
<head>
    <meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate" />
<meta http-equiv="Pragma" content="no-cache" />
<meta http-equiv="Expires" content="0" />
<meta name="description" content="Example of loading MSAViewer from a string">
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>Acidic patch binders</title>
</head>
<body>
    <script
  src="https://code.jquery.com/jquery-3.7.0.min.js"
  integrity="sha256-2Pmvv0kuTBOenSvLm6bvfBSSHrUJ+3A7x6P5Ebd07/g="
  crossorigin="anonymous"></script>
    <script src="/js/msa.min.js"></script>
    <script src="/js/ngl.js"></script>

    <div style="width: 100%; display: table;">
    
    <div style="display: table-row">
        <div id="msa_0" style="width: 600px; display: table-cell;"> MSA </div>
        
        <div id="cluster_panel" style="display: table-cell; vertical-align:top"> 
        To highlight residues, double-click on them in the alignment. 
        <br>You can also highlight aligned positions by clicking on the residue number below the sequence logo. 
        <br>To highlight multiple residues, hold down the CTRL button.
            <br>
        <br>Select cluster
           <span style="width: 20px; display: inline-block;"> </span>
        </div>
    </div>
    <div style="display: table-row">
        <div id="msa" style="width: 600px; display: table-cell;"> MSA </div>
        <div id="viewport" style="display: table-cell; height:500px;"> 3D view </div>
    </div>
</div>
<!--     <div id="msa">Loading Multiple Alignment...</div>
    <div id="viewport", style="padding: 0px; min-height:500px;"></div> -->
<script>
    
    function waitForElm(selector) {
    return new Promise(resolve => {
        if (document.querySelector(selector)) {
            return resolve(document.querySelector(selector));
        }

        const observer = new MutationObserver(mutations => {
            if (document.querySelector(selector)) {
                observer.disconnect();
                resolve(document.querySelector(selector));
            }
        });

        observer.observe(document.body, {
            childList: true,
            subtree: true
        });
    });
}
    
// loading logo from unique
     var opts = {
  el: document.getElementById("msa_0"),
  importURL: "./dat/acidic_clusters_unique.fasta",
  // colorscheme: {"scheme": "clustal"},
  vis:{seqlogo:true,
       sequences:false,
       markers: false,
       labels: true,
    labelName: false,
    labelId: true,
    // labelPartition: true,
      labelCheckbox:false},
    zoomer: {
    // general
    alignmentHeight: 0,
    columnWidth: 15,
    rowHeight: 0,
    autoResize: true,} // only for the width
};
    var m_logo = msa(opts);

 // m.g.selcol тут искать всю выборку
    // m_logo.render();
// loading main   
 var opts = {
  el: document.getElementById("msa"),
  importURL: "./dat/acidic_clusters.fasta",
  colorscheme: {scheme: "taylor",showLowerCase: false},
  vis:{seqlogo:false,
       labels: true,
    labelName: true,
    labelId: true,
    // labelPartition: true,
      labelCheckbox:false},
};
    var m = msa(opts);
    m.g.on("_rendered", function(){console.log(1)})
    // m.on('ready',function(){console.log(1)})
 // m.g.selcol тут искать всю выборку
    m.render();
    
    var num_clusters = 0;

// adding tickboxes
    function decorate_msa(){
        $('#msa .biojs_msa_labelblock').ready(function(){
            
            $('#msa .biojs_msa_headers').css("width","245px");
            var legend = $('#msa .biojs_msa_headers').children()[0].childNodes[1]
            legend.textContent = 'Hist. Ac.P. Int. Licor.'

            var rows = $('#msa .biojs_msa_labels');
            rows.css("width","240px");

            for (let item of rows) {
                // console.log(item.title);
                // let visBox = document.createElement("input",type='checkbox');
                // visBox.type = "checkbox";
                // visBox.setAttribute("id", item.title+':vis_box');
                // visBox.setAttribute('checked', true);
                // item.children[0].after(visBox);

                let histBOX = document.createElement("input",type='checkbox');
                histBOX.type = "checkbox";
                histBOX.setAttribute('checked', true);
                histBOX.setAttribute("id", item.title+':hist_vis_box');
                console.log(item.title);
                histBOX.setAttribute("class", 'hist_vis_box');
                item.children[0].after(histBOX);

                let APBOX = document.createElement("input",type='checkbox');
                APBOX.type = "checkbox";            
                APBOX.setAttribute("id", item.title+':AP_vis_box');
                APBOX.setAttribute("class", 'AP_vis_box');
                item.children[1].after(APBOX);
                
                num_clusters = parseInt(item.title.substring(item.title.indexOf('clust_')+6))
                let intBOX = document.createElement("input",type='checkbox');
                intBOX.type = "checkbox";
                intBOX.setAttribute("id", item.title+':int_vis_box');
                intBOX.setAttribute("class", 'int_vis_box ' + 'cluster_' + String(num_clusters));
                item.children[2].after(intBOX);
                
                
                

                // let cpkBOX = document.createElement("input",type='checkbox');
                // cpkBOX.type = "checkbox";
                // cpkBOX.setAttribute("id", item.title+':cpk_vis_box');
                // item.children[3].after(cpkBOX);
            }
            var clust_lbl = {
                             1:'PO',
                             2:'DU1',
                             3:'DU2',
                             4:'UD1',
                             5:'UD2',
                             6:'UD3',
                             7:'UD4',                
            };
            for (let cluster of [...Array(num_clusters).keys()]) {
                cluster = cluster + 1;
                let clusterbox = document.createElement("input",type='checkbox');
                clusterbox.type = "checkbox";
                clusterbox.setAttribute("id", cluster+':cluster_box');
                var lbl = document.createElement("span");
                lbl.innerHTML = clust_lbl[parseInt(cluster)];
                $('#cluster_panel').append(lbl);
                $('#cluster_panel').append(clusterbox);
                
                $('[id="'+cluster+':cluster_box'+'"]').change(function(data) {
                    console.log(data);
                    $('.'+'cluster_' + cluster).prop('checked',$('[id="'+cluster+':cluster_box'+'"]').is(':checked')).trigger("change");
                })
            }

            $(':checkbox').change(function(data) {

                var checkboxid = data.target.id;
                console.log(checkboxid);
                var state = $('[id="'+checkboxid+'"]').is(':checked');
                var pdb_id = checkboxid.split(':')[0];
                var kind = checkboxid.split(':')[3];
                // console.log(checkboxid);
                if (kind == 'AP_vis_box') {
                    window.ap_reprs[pdb_id].setVisibility(state);
                } else if (kind == 'int_vis_box') {
                    window.int_reprs[pdb_id].setVisibility(state);
                // } else if (kind == 'cpk_vis_box') {
                //     window.int_lic_reprs[pdb_id].setVisibility(state);
                } else if (kind == 'hist_vis_box') {
                    window.hist_reps[pdb_id].setVisibility(state);
                } 





            });
            $('#msa_0 .biojs_msa_headers').ready(function(){
                $('#msa_0 .biojs_msa_headers')[0].children[0].childNodes[0].textContent=''
                $('#msa_0 .biojs_msa_headers').css("width","245px");
                var item = $('#msa_0 .biojs_msa_headers')[0];
                let histBOX = document.createElement("input",type='checkbox');
                histBOX.type = "checkbox";
                histBOX.setAttribute('checked', true);
                histBOX.setAttribute("id", 'all_hist_vis_box');
                item.children[0].after(histBOX);
                $('#all_hist_vis_box').change(function(data) {
                    console.log(data);
                    $('.hist_vis_box').prop('checked',$('#all_hist_vis_box').is(':checked')).trigger("change");
                })

                let APBOX = document.createElement("input",type='checkbox');
                APBOX.type = "checkbox";            
                APBOX.setAttribute("id", 'all_AP_vis_box');
                item.children[1].after(APBOX);
                $('#all_AP_vis_box').change(function(data) {
                    console.log(data);
                    $('.AP_vis_box').prop('checked',$('#all_AP_vis_box').is(':checked')).trigger("change");
                })

                let intBOX = document.createElement("input",type='checkbox');
                intBOX.type = "checkbox";
                intBOX.setAttribute("id", 'all_int_vis_box');
                item.children[2].after(intBOX);
                $('#all_int_vis_box').change(function(data) {
                    console.log(data);
                    $('.int_vis_box').prop('checked',$('#all_int_vis_box').is(':checked')).trigger("change");
                })
            })

        })
    }
    // loading structures
    var structures_info;
    var firstLoaded = false;
    
    var structures_info = (function () {
        var json = null;
        $.ajax({
            'async': false,
            'global': false,
            'url': './dat/ap_structues.json',
            'dataType': "json",
            'success': function (data) {
                json = data;
            }
        });
        return json;
    })(); 
    
    // $.getJSON('./static/ap_structues.json', function(json) {
    //   structures_info = json;
    // });
    load_all_structures();
    function load_all_structures(){
      window.stage = new NGL.Stage("viewport",{ backgroundColor:"#FFFFFF" });
      // window.addEventListener( "resize", function( event ){
      //   stage.handleResize();
      // }, false );
      window.stage.setParameters({cameraType: "orthographic"})
      window.structures = {};
      window.hist_reps = {};
      window.hl_reps = [];  
      window.ap_reprs = {};
      window.int_reprs = {};
      window.int_sels = {};
      window.base_colors = {};
      
      var cur_index = 0;  
      for (const [pdb_id, info_dict] of Object.entries(structures_info)) {
          
          var H3_sele = " :" + info_dict['h3segid'].join(' :')
          var H4_sele = " :" + info_dict['h4segid'].join(' :')
          var H2A_sele = " :" + info_dict['h2asegid'].join(' :')
          var H2B_sele = " :" + info_dict['h2bsegid'].join(' :')
          var dna_sele = " :" + info_dict['dnasegid'].join(' :')
          var nucl_sele = H3_sele + H4_sele + H2A_sele + H2B_sele
          // console.log(nucl_sele);
          var ap_sele = ''
          for (let item of info_dict['AP_sel']) { 
              ap_sele = ap_sele + String(item[1])+':'+item[0]+' ';
          }
          
          var int_sele = String(info_dict['int_start_resid'])+ "-" + String(info_dict['int_start_resid']+info_dict['int_len']-1)+ ":" + info_dict['int_segid']   
          int_sels[pdb_id] = int_sele;
          var side =  info_dict['side']
          
          var schemeId = NGL.ColormakerRegistry.addSelectionScheme([
              ['#020AED', H3_sele],
              ["green", H4_sele],
              ['#E0F705', H2A_sele],
              ['#CE0000', H2B_sele],
              ["grey", "*"]
            ], pdb_id+"colormap");
          if (cur_index == Object.keys(structures_info).length-1 ){
              load_structure(pdb_id,schemeId,nucl_sele,int_sele,ap_sele,true);
          } else {
              load_structure(pdb_id,schemeId,nucl_sele,int_sele,ap_sele,false);
          }
          
          cur_index = cur_index + 1;
          if (firstLoaded) {window.stage.autoView();firstLoaded = true}
 
          }
        // decorate_msa();
        // HIGHLIGHT RESIDUE
        function update_highlight(){
            for (let item of window.hl_reps) {
            item.dispose();
            }        
            window.hl_reps = [];
            
            for (let item of m.g.selcol.models) { 
                // console.log(item.attributes);
                if (item.attributes.type == 'pos') {
                    var pdb_id = m.seqs.models[item.attributes.seqId].attributes.name.split(':')[0];
                    var pos = item.attributes.xStart;
                    var segid = structures_info[pdb_id]['int_segid'];
                    var sele = 'sidechain and ' + String(structures_info[pdb_id]['int_start_resid']+ pos) +':'+segid;
                    var selection = new NGL.Selection(sele);
                    var radius = 4;
                    var atomSet  = window.structures[pdb_id].structure.getAtomSetWithinSelection( selection, radius );
                    var atomSet2 = window.structures[pdb_id].structure.getAtomSetWithinGroup( atomSet );
                    window.hl_reps.push(window.structures[pdb_id].addRepresentation('licorice', {"sele": sele , "radius":0.3}));
                    window.hl_reps.push(window.structures[pdb_id].addRepresentation('licorice', {"sele": "not :" +segid +' and (' + atomSet2.toSeleString() +')', "radius":0.1}));
                    
                } else if (item.attributes.type == 'column') {
                    for (const [pdb_id, info_dict] of Object.entries(structures_info)) {
                        var pos = item.attributes.xStart;
                        var sele = 'sidechain and ' + String(structures_info[pdb_id]['int_start_resid']+ pos) +':'+structures_info[pdb_id]['int_segid'];                        
                        window.hl_reps.push(window.structures[pdb_id].addRepresentation('licorice', {"sele": sele, "radius":0.3}));
                    } 
                } else if (item.attributes.type == 'row') {
                    var pdb_id = m.seqs.models[item.attributes.seqId].attributes.name.split(':')[0];
                    window.hl_reps.push(window.structures[pdb_id].addRepresentation('licorice', {
                                                                                     "sele": int_sels[pdb_id], "radius":0.3}));
                }
            }     
        }
        
        
        m.g.on("residue:click", function(data){
            update_highlight();
        });
        // HIGHLIGHT same residues
        m.g.on("column:click", function(data){
            update_highlight();
        })
        
        m.g.on("row:click", function(data){
            update_highlight();
        })
        
     }
    

        
     function load_structure(pdb_id,colorscheme,nucl_sele,int_sele,ap_sele,decorate){
         
          var orientationMatrix = stage.viewerControls.getOrientation();
          
          window.stage.loadFile("/AP/"+pdb_id+".pdb").then(function (nucl) {
              
               // if (side == 2) {nucl.setRotation([0,0,0])}
              

              if (pdb_id=='1ZLA') {
                                   nucl.addRepresentation('cartoon', {
                     "sele": 'nucleic', color: 'silver',"aspectRatio": 1.5,opacity:0.3,
                      'radiusScale': 2.1,'radiusType': 'sstruc','side':'front',"capped": true,
                      'subdiv': 10,'diffuseInterior': false,'useInteriorColor': false });
                                  }
              window.hist_reps[pdb_id] = nucl.addRepresentation('cartoon', {
                     "sele": nucl_sele, color: colorscheme,"aspectRatio": 1.5,
                      'radiusScale': 2.1,'radiusType': 'sstruc',"capped": true,
                      'subdiv': 10,'diffuseInterior': false,'useInteriorColor': false });
              window.int_reprs[pdb_id] = nucl.addRepresentation('cartoon', {
                 "sele": int_sele, "colorScheme": 'occupancy',"colorScale":'rwb',"colorReverse":false,"aspectRatio":1, 'radiusType':'bfactor',"radiusScale":1,"radiusSegments":1,"capped":0,"visible":false });
              // window.int_lic_reprs[pdb_id] = nucl.addRepresentation('licorice', {
              //    "sele": int_sele, "radius":0.3,"visible":false });
              // nucl.addRepresentation('base', {
              //    "sele": 'nucleic', "color": color});
              window.ap_reprs[pdb_id] = nucl.addRepresentation('licorice',{"sele":ap_sele,"radius":0.3,"visible":false})
              console.log('loaded '+pdb_id);
              window.structures[pdb_id] = nucl;
              if (decorate) {decorate_msa();};
              }); 
  }
  
    
</script>

</body>
</html>
<!-- 6Y5D
7E8I
7UV9
5E5A
7CCR
7U0G
8SPS -->
