dir = getDirectory("Choose a Directory"); 
print(dir);
setBatchMode(false);
   count = 0;
   countFiles(dir);
   n = 0;
   processFiles(dir);
   //print(count+" files processed");
   
   function countFiles(dir) {
      list = getFileList(dir);
      for (i=0; i<list.length; i++) {
          if (endsWith(list[i], "/"))
              countFiles(""+dir+list[i]);
          else
              count++;
      }
  }

   function processFiles(dir) {
      list = getFileList(dir);
      //print(list.length);
      for (i=0; i<list.length; i++) {
          if (endsWith(list[i], "/"))
              processFiles(""+dir+list[i]);
          else {
            //print(list[i]); 
	 showProgress(n++, count);
             path = dir+list[i];
	//print(path);
             processFile(path,list[i]);
	//print(path, list[i]);
         }
      }
  }

  function processFile(path, figName) {
	//print(path, figName);
       if (endsWith(path, ".JPG")) {
		//print(path);
           		open(path);
		//print(open);
		segmentation();
		//print(segmentation);
		saveAs("Results", dir + "\\" + figName+".txt");
                }
  }
function segmentation (){
x=getTitle();
//print(x);
run("8-bit");
run("Find Edges");
//run("Threshold...");
setAutoThreshold("Default dark");
setThreshold(82, 255);
run("Convert to Mask");
run("Make Binary");
run("Fill Holes");
run("Particles8 ", "white show=Particles overwrite");
// look for Pixels, not Area!
ar=0;
for (i=0; i<nResults; i++) { 
ca = getResult('Pixels', i);
if (ca>ar) ar=ca;
}
run("Particles8 ", "white show=Particles filter minimum=" + ar +" maximum="+ 
ar +" overwrite");
run("Convert to Mask");
run("Particles8 ", "white show=Particles overwrite");
// look for Pixels, not Area!
ar=0;
for (i=0; i<nResults; i++) { 
ca = getResult('Pixels', i);
if (ca>ar) ar=ca;
}

run("Particles8 ", "white show=Particles filter minimum=" + ar +" maximum="+ 
ar +" overwrite");
run("Fill Holes");
run("Create Selection");
if (selectionType==-1){
      exit("Area selection required");
}
  List.setMeasurements;
  //print(List.getList); // list all measurements
  x = List.getValue("X");
  y = List.getValue("Y");
  a = List.getValue("Major");
  b = List.getValue("Minor");
  angle = List.getValue("Angle");
  getVoxelSize(w, h, d, unit);
  drawEllipse(x/w, y/w, (a/w)/2, (b/w)/2, angle);
 // exit;

  function drawEllipse(x, y, a, b, angle) {
      autoUpdate(false);
      setLineWidth(2);
      beta = -angle * (PI/180);
      for (i=0; i<=360; i+=2) {
          alpha = i*(PI/180) ;
          X = x + a*cos(alpha)*cos(beta) - b*sin(alpha)*sin(beta);
          Y = y + a*cos(alpha)*sin(beta) + b*sin(alpha)*cos(beta);
          if (i==0) moveTo(X, Y); else lineTo(X,Y);
          if (i==0) {ax1=X; ay1=Y;}
          if (i==90) {bx1=X; by1=Y;}
          if (i==180) {ax2=X; ay2=Y;}
          if (i==270) {bx2=X; by2=Y;}
      }

 }

run("Set Scale...", "distance=91.489 known=10 pixel=1 unit=micron");
run("Set Measurements...", "  min centroid bounding fit shape feret's limit redirect=None decimal=3");

run("Analyze Particles...", "size=0-Infinity circularity=0.00-1.00");
}
