Welcome to the StatGen_practicals wiki!
# Prepare the data and software

## Step1. Download the R and R studio

### a. Download

[R](https://cran.rstudio.com/)

[R studio](https://posit.co/download/rstudio-desktop/)

### b. Open the R studio and install the following R packages **(RStudio Console)**

    install.packages("data.table") # for faster input and output
    install.packages("CMplot") # for Manhattan plot
    library(data.table)
    library(CMplot)


## Step2. Open the terminal and download the datasets
### a. Open the terminal

RStudio `View > Move Focus to Terminal`

### b. Check if you have installed git **(Terminal)**

    git -v
    # git version XX (Apple Git-154) or git version XX.windows.X


### c1. If you have installed git, please download the datasets by git **(Terminal)**
    git clone https://github.com/RL-m/StatGen_practicals.git
    cd StatGen_practicals # Enter the folder
    ls # list all the datasets in the folder

<img width="202" alt="Screenshot 2025-07-09 at 13 37 08" src="https://github.com/user-attachments/assets/cefd4774-aa77-40ec-9d80-7a1cf495c447" />

### c2. If you haven't installed git, please download the datasets by OneDrive link

[StatGen_practicals.tar.gz](https://westlakeu-my.sharepoint.com/:u:/g/personal/maruilei_westlake_edu_cn/ETHDHP-85N9KqVGKvOMjV7cBZgLm3xX-SYRMr2K6W6dOjg?e=ALXH9Z)

    tar -xzvf /Where/you/download/StatGen_practicals.tar.gz $ Unpack the folder
    cd /Where/you/download/StatGen_practicals # Enter the folder
    ls # list all the datasets in the folder

For Mac, the downloaded items are usually in /Users/yourname/Downloads

For Windows, the downloaded items are usually in /c/Users/yourname/Downloads


## Step3. Download the matched version of GCTA and SMR

[GCTA](https://yanglab.westlake.edu.cn/software/gcta/#Download)

[SMR](https://yanglab.westlake.edu.cn/software/smr/#Download)

For **Mac chip** please choose the "arm64" version.

Unzip the package.

## Step4. Organize your working address
### a. Prepare your working address **(Terminal)**
    pwd # shown bellow is your working address
    # Usually, it is /Users/yourname/StatGen_practicals for Mac and /c/Users/yourname/StatGen_practicals for Windows

### b. Move the downloaded software to the target folder **(Terminal)**

GCTA:

    # For Mac
    mv /Users/yourname/Downloads/gcta-1.94.1-macOS-x86_64/gcta-1.94.1 ./gcta.exe
    # For **Mac chip**, the GCTA software you downloaded will be /Users/yourname/Downloads/gcta-1.94.1-macOS-arm64/gcta-1.94.1
    
    # For Windows
    mv /c/Users/yourname/Downloads/gcta-1.94.1-Win-x86_64/exe/* ./
    mv ./gcta64.exe ./gcta.exe

SMR:

    # For Mac
    mv /Users/yourname/Downloads/smr_Mac ./smr.exe
    
    # For Windows
    mv /c/Users/yourname/Downloads/smr-1.3.1-win-x86_64/* ./
    mv ./smr-1.3.1-win.exe ./smr.exe

### c. Set RStudio working address **(RStudio Console)**

**Please notice that for convenience, the following practicals will all be finished in the RStudio Console by R code.**

    workAdd <- "/Your/address/StatGen_practicals" # copy your working address from Terminal `pwd` output (Step4.a.)
    # For Windows, please change the "/c/Users" prefix to "C:/Users"
    
    setwd(workAdd) # enter the working address in R

Simultaneously, you can set the RStudio file window to the working address by:

`Files > More file commands > Go To Working Directory`

### d. Make sure GCTA and SMR are able to run **(RStudio Console)**

    system("./gcta.exe")
    system("./smr.exe")

Pass the permissions:

For Mac: `System Settings > Privacy & Security > Allow Anyway > Open Anyway`

<img width="472" alt="Screenshot 2025-07-09 at 09 22 32" src="https://github.com/user-attachments/assets/ae203ff7-aa77-45e4-ba44-badf6801841f" />

For Windows: `More info > Run anyway`

If GCTA and SMR are running normally, the following messages will show up.

<img width="400" alt="Screenshot 2025-07-10 at 10 01 23" src="https://github.com/user-attachments/assets/d52728aa-07da-4679-be9b-24b326fa9889" />

<img width="400" alt="Screenshot 2025-07-10 at 10 01 32" src="https://github.com/user-attachments/assets/9c80b25c-17fb-479d-bd59-f25e06c0c6a5" />


# Practice 1. Simulate GWAS data **(GCTA)**
## Step1. Randomly select 5 causal variants with non-zero effect sizes on the phenotype


### a. Unpack and read the genotype package
    system("tar -xzvf 1kgp3_EUR.hg38.hp3.tar.gz") # unpack the genotype files
    
    # read the genotype file
    bim <- fread("1kgp3_EUR.hg38.hp3.bim",col.names=c("CHR","SNP","MOL","POS","A1","A2"), colClasses=c(rep("character",6)))
    head(bim)

<img width="252" alt="Screenshot 2025-07-09 at 08 41 02" src="https://github.com/user-attachments/assets/c006d869-3ce0-41ff-96dc-c08a3737f786" />

### b. Select causal variants and write the causal SNP list

    # Select causal variants
    snpList <- sample(bim$SNP,5)
    # Write the snp list into a text file
    fwrite(as.data.frame(snpList),"test.snplist",row.names=F,col.names=F,quote=F)

File "test.snplist" will be generated.

## Step2. Simulate phenotype
    system("./gcta.exe --bfile 1kgp3_EUR.hg38.hp3 --simu-qt --simu-causal-loci test.snplist --simu-hsq 0.2 --out test")
    
Files "test.par", "test.phen", and "test.log" will be generated.

## Step3. Compute grm
    system("./gcta.exe --bfile 1kgp3_EUR.hg38.hp3 --make-grm --out test")

Files "test.grm.N.bin", "test.grm.bin", "test.grm.id", and a new "test.log" will be generated.

## Step4. Calculate heritability
    system("./gcta.exe --grm test --pheno test.phen --reml --out test")

File "test.hsq" will be generated. V(G)/Vp is the SNP-based heritability.

<img width="235" alt="Screenshot 2025-07-09 at 13 32 04" src="https://github.com/user-attachments/assets/041e98fb-bde9-443f-ad3d-dab7570d8fa3" />


## Step5. Calculate PCA
    system("./gcta.exe --grm test --pca --out test")

Files "test.eigenval" and "test.eigenvec" will be generated. 

    # plot PCA results #
    pc <- fread("test.eigenvec")
    plot(pc$V3, pc$V4, xlab="PC1", ylab="PC2", col="red")
    pc  <-  fread("test.eigenvec")
    all <- fread("1kgp3_all.hg38.hp3.eigenvec")
    all$col <- "grey"
    all$col[all$V1 %in% pc$V1] <- "red"
    plot(all$V3, all$V4, xlab="PC1", ylab="PC2", col=all$col)

Here, we used the eigen vectors to plot the population stratifications. The red circles represent our samples (from Europe) and the grey circles represent all samples in 1000 Genome project 3 (including Europeans, Afrians, East Asians, Americans, and South Asians). 

<img width="593" alt="Screenshot 2025-07-09 at 09 58 42" src="https://github.com/user-attachments/assets/8a20ca10-2153-4a92-a226-67a8ba5d8358" />

## Step6. Perform GWAS
### a. Make sparse grm
    system("./gcta.exe --grm test --make-bK-sparse 0.05 --out test")

File "test.grm.sp" will be generated. All genetic correlations < 0.05 will be set to 0.

### b. Run fastGWA
    system("./gcta.exe --bfile 1kgp3_EUR.hg38.hp3 --grm-sparse test --fastGWA-mlm --pheno test.phen --out test")

File "test.fastGWA" will be generated, which contain the GWAS summary statistics in the COJO format.

<img width="821" alt="Screenshot 2025-07-09 at 16 23 31" src="https://github.com/user-attachments/assets/ebcff418-3a9a-4e0f-8063-462d8b793ed5" />

### c. Generate Manhattan plot
    library(CMplot)
    # read in summary statistics
    gwas.df<- fread("test.fastGWA",header=T)
    plot.use <- as.data.frame(gwas.df[,c("SNP","CHR","POS","P")])
    class(plot.use$CHR) <- "integer"
    class(plot.use$POS) <- "integer"
    class(plot.use$P) <- "numeric"
    # draw and output
    CMplot(plot.use,type="p",plot.type="m",LOG10=TRUE,threshold=5e-8,threshold.lty=2,
       threshold.lwd=1, threshold.col="grey",amplify=TRUE,file="pdf",
       file.output=TRUE,verbose=TRUE,width=14,height=6,cex=0.8,band=0.5,
       col=c("#0073C2FF","#EFC000","#868686","#CD534C","#7AA6DC","#003C67","#8F7700","#3B3B3B","#A73030","#4A6990"))

File "Rect_Manhtn.P.pdf" will be generated. There could be < 5 peaks on the plot because the effect sizes of the causal variants were different (randomly generated from the standard normal distribution).

<img width="1086" alt="Screenshot 2025-07-09 at 14 24 30" src="https://github.com/user-attachments/assets/9bd8da4f-cab9-44ab-87eb-262ccd91dca9" />



# Practice 2. Integrate GWAS and eQTL data to identify candidate genes **(SMR)**
## Step1. Prepare the input datasets
### a. eQTL data
    system("tar -zxvf westra_eqtl.tar.gz")

There are 3 files that store the eQTL information:

westra_eqtl.besd, westra_eqtl.epi, and westra_eqtl.esi.

### b. GWAS summary statistics

There are 4 pre-processed GWAS summary statistics in the COJO format:

CAD_NG2022.hp3.ma, HDL_2021_Nature.hp3.ma, IQ_NG_2018.hp3.ma, and LDL_2021_Nature.hp3.ma.

These files contain the GWAS summary statistics of LDL (Low-density lipoprotein), CAD (Coronary artery disease), HDL (High-density lipoprotein), and IQ (Intelligence quotient).

### c. LD reference
There are 3 files that store the genotype information and provide LD reference for SMR:

1kgp3_EUR.hg38.hp3.bed, 1kgp3_EUR.hg38.hp3.bim, and 1kgp3_EUR.hg38.hp3.fam.

## Step2. Run SMR
    # Please choose any of the 4 GWAS data for the analysis
    system("./smr.exe --bfile 1kgp3_EUR.hg38.hp3 --gwas-summary CAD_NG2022.hp3.ma --beqtl-summary westra_eqtl --maf 0.01 --thread-num 2 --out CAD")

CAD.smr and CAD.snp_failed_freq_ck.list will be generated

## Step3. Analyze the results 
    smr <- fread("CAD.smr",header=T)
    significant_genes <- smr[smr$p_SMR<0.05/nrow(smr) & smr$p_HEIDI > 0.01,]
    dim(significant_genes) # the result could be different if you use other traits
    # [1] 50 21

50 genes associated with CAD passed the SMR test.

We had drawn the following figure to show one of the significant CAD candidate genes: _NT5C2_

<img width="416" alt="image" src="https://github.com/user-attachments/assets/7facb283-a93a-4d7a-86e9-d5707c667712" />


We can also check the results on [SMR Portal](https://yanglab.westlake.edu.cn/smr-portal/database).

# Practice 3. Unravel causal associations between complex traits **(GCTA-GSMR module)**
## Step1. Generate exposure and outcome files
    exposure <- data.frame(ID="LDL",ADD=paste0(workAdd,"/LDL_2021_Nature.hp3.ma"))
    outcome <- data.frame(ID=c("CAD","HDL","IQ"),ADD=paste0(workAdd,c("/CAD_NG2022.hp3.ma","/HDL_2021_Nature.hp3.ma","/IQ_NG_2018.hp3.ma")))
    fwrite(exposure,paste0(workAdd,"/exposure.list"),quote=F,sep="\t",col.names=F,row.names=F)
    fwrite(outcome,paste0(workAdd,"/outcome.list"),quote=F,sep="\t",col.names=F,row.names=F) 

File "exposure.list" and "outcome.list" will be generated, containing the trait names and addresses to the summary statistics. Set LDL as the exposure. Set CAD, HDL, and IQ as outcomes.

## Step2. Run GSMR bidirection
    system("./gcta.exe --bfile 1kgp3_EUR.hg38.hp3 --gsmr-file exposure.list outcome.list --gsmr-direction 2 --effect-plot --thread-num 4 --out test")

test.gsmr, test.eff_plot.gz, test.freq.badsnps, and test.badsnps files will be generated.

## Step3. Plot SNP effects
    source("gsmr_plot.r")
    gsmr_data = read_gsmr_data("test.eff_plot.gz")
    gsmr_summary(gsmr_data)
    plot_gsmr_effect(gsmr_data, expo_str = "LDL", outcome_str = "CAD", "red") # LDL as the exposure, CAD as the outcome
    plot_gsmr_effect(gsmr_data, expo_str = "LDL", outcome_str = "HDL", "red") # LDL as the exposure, HDL as the outcome
    plot_gsmr_effect(gsmr_data, expo_str = "LDL", outcome_str = "IQ", "red") # LDL as the exposure, IQ as the outcome

<img width="302" alt="Screenshot 2025-07-09 at 14 08 28" src="https://github.com/user-attachments/assets/cf6468a3-4557-487a-a6ee-46c81971ff86" />

<img width="294" alt="Screenshot 2025-07-09 at 14 09 10" src="https://github.com/user-attachments/assets/aceb880c-9830-4c08-b933-5126f7594036" />

<img width="299" alt="Screenshot 2025-07-09 at 14 09 48" src="https://github.com/user-attachments/assets/66f82627-bdc2-4305-807b-63ecbae24fbb" />

# Extensions
## 1. Explore the SMR portal
## 2. Explore other modules of GCTA and SMR

e.g. GCTA-COJO, GCTA-mBat-combo, vQTL, SMR-multi, SMR-query, etc. 


