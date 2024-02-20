# ChineseEEG: A Chinese Linguistic Corpora EEG Dataset for Semantic Alignment and Neural Decoding

## Introduction

"ChineseEEG" (Chinese Linguistic Corpora EEG Dataset) contains high-density EEG data and simultaneous eye-tracking data recorded from 10 participants, each silently reading Chinese text for about 11 hours. This dataset further comprises pre-processed EEG sensor-level data generated under different parameter settings, offering researchers a diverse range of selections. Additionally, we provide embeddings of the Chinese text materials encoded from BERT-base-chinese model, which is a pre-trained NLP specifically used for Chinese, aiding researchers in exploring the alignment between text embeddings from NLP models and brain information representations.

## Participant Overview

In total, data from 10 participants were used (18-24 years old, averaged 20.68 years old, and 5 males). No participants reported neurological or psychiatric history. All participants are right-handed and have normal or corrected-to-normal vision. 

## Experiment Materials

The experimental materials consist of two novels in Chinese, both in the genre of children's literature. The first is **The Little Prince** and the second is **Garnett Dream**. 
For **The Little Prince**, the preface was used as material for the practice reading phase. The main body of the novel was then used for seven sessions in the formal reading phase. The first six sessions each included 4 chapters of the novel, while the seventh session included the last two chapters.
For **Garnett Dream**, the first 18 chapters were used for 18 sessions in the formal reading stage, with each session including a complete chapter. 

To properly present the text on the screen during the experiments, the content of each session was segmented into a series of units, with each unit containing no more than 10 Chinese characters. These segmented contents were saved in Excel (.xlsx) format for subsequent usage. During the experiment, three adjacent units from each session's content will be displayed on the screen in three separate lines, with the middle line highlighted for the participant to read. 
In summary, a total of 115,233 characters (24,324 in **The Little Prince** and 90,909 in **Garnett Dream**), of which 2985 characters were unique, were used as experimental stimuli in ChineseEEG dataset. 

The original and segmented novels are saved in the `derivatives/novels` folder. The `segmented_novel` folder in `novels` folder contains two types of Excel files: one type of file has names ending with "display," while the other type does not contain this suffix. The former stores units that have been segmented; the latter includes units that have been reassembled according to the experimental presentation format. These files ending with "display" will be used to support the execution of relevant code, in order to achieve effective stimulus presentation in the experiment. 

The code for generating these two types of files, as well as the code for experimental presentation, can be found in the GitHub repository: https://github.com/ncclabsustech/Chinese_reading_task_eeg_processing.

## Experiment Procedures

Participants were tasked with reading a novel and were required to keep their heads still and keep their gaze on the highlighted (red) Chinese characters moving across the screen, reading at a pace set by the program. They were required to read an entire novel in multiple runs within a single session. Each run is divided into two phases: the eye-tracker calibration phase and the reading phase.

The eye-tracker calibration phase is at the beginning of each run, requiring participants to keep their gaze at a fixation point, which sequentially appeared at the four corners and the center of the screen.

In the reading phase, the screen initially displayed the serial number of the current chapter. Subsequently, the text appeared with three lines per page, ensuring each line contained no more than ten Chinese characters (excluding punctuation). On each page, the middle line was highlighted as the focal point, while the upper and lower lines were displayed with reduced intensity as the background. Each character in the middle line was sequentially highlighted with red color for 0.35 s, and participants were required to read the novel content following the highlighted cues.

For detailed information about the experiment settings and procedures, please refer to our paper at https://doi.org/10.1101/2024.02.08.579481.

## Markers

To precisely co-register EEG segments with individual characters during the experiment, we marked the EEG data with triggers. 

- EYES: Eyetracker starts to record
- EYEE: Eyetracker stops recording
- CALS: Eyetracker calibration starts
- CALE: Eyetracker calibration stops
- BEGN: EGI starts to record
- STOP: EGI stops recording
- CHxx：Beginning of specific chapter (Numbers correspond with chapters) 
- ROWS: Beginning of a row
- ROWE: End of a row
- PRES：Beginning of the preface
- PREE：End of the preface

## Data Record

The raw EEG data has a sampling rate of 1 kHz, while the filtered data and pre-processed data has a sampling rate of 256 Hz.

### Data Structure

The dataset is organized following the EEG-BIDS specification using the MNE-BIDS package. The dataset contains some regular BIDS files, 10 participants’ data folders, and a derivatives folder. The stand-alone files offer an overview of the dataset: i) dataset_description.json is a JSON file depicting the information of the dataset, such as the name, dataset type and authors; ii) participants.tsv contains participants’ information, such as age, sex, and handedness; iii) participants.json describes the column attributes in participants.tsv; iv) README.md contains a detailed introduction of the dataset.
Each participant’s folder contains two folders named ses-LittlePrince and ses-GarnettDream, which store the data of this
participant reading two novels, respectively. Each of the two folders contains a folder eeg and one file sub-xx_scans.tsv. The tsv
file contains information about the scanning time of each file. The eeg folder contains the source raw EEG data of several runs,
channels, and marker events files. Each run includes an eeg.json file, which encompasses detailed information for that run,
such as the sampling rate and the number of channels. Events are stored in events.tsv with onset and event ID. The EEG data
is converted from raw metafile format (.mff file) to BrainVision format (.vhdr, .vmrk and .eeg files) since EEG-BIDS is not
officially compatible with .mff format.
The derivatives folder contains six folders: eyetracking_data, filtered_0.5_80, filtered_0.5_30, preproc, novels, and text_embeddings. The eyetracking_data folder contains all the eye-tracking data. Each eye-tracking data is formatted in a
.zip file with eye moving trajectories and other parameters like sampling rate saved in different files. The filtered_0.5_80
folder and filtered_0.5_30 folder contain data that has been processed up to the pre-processing step of 0.5-80 Hz and 0.5-30
Hz band-pass filtering respectively. This data is suitable for researchers who have specific requirements and want to perform
customized processing on subsequent pre-processing steps like ICA and re-referencing. The preproc folder contains minimally
pre-processed EEG data that is processed using the whole pre-processing pipeline. It includes four additional types of files
compared to the participants’ raw data folders in the root directory: i) bad_channels.json contains bad channels marked during
bad channel rejection phase. ii) ica_components.npy stores the values of all independent components in the ICA phase. iii)
ica_components.json includes the independent components excluded in ICA (the ICA random seed is fixed, allowing for
reproducible results). iv) ica_components_topography.png is a picture of the topographic maps of all independent components,
where the excluded components are labeled in grey. The novels folder contains the original and segmented text stimuli materials. The original novels are saved in .txt format and the segmented novels corresponding to each experimental run are saved in Excel (.xlsx) files. The text_embeddings folder contains embeddings of the two novels. The embeddings corresponding to each experimental run are stored in NumPy (.npy) files  

For an overview of the structure, please refer to our paper at https://doi.org/10.1101/2024.02.08.579481.

### Pre-processing

For the pre-processed data in the derivatives folder, we only did minimal pre-processing to retain most useful information. The pre-processing steps include data segmentation, downsampling, filtering, bad channel interpolation, ICA, averaging. 

During the data segmentation phase, we only retained data from the formal reading phase of the experiment. Based on the
event markers during the data collection phase, we segmented the data, removing sections irrelevant to the formal experiment
such as calibration and preface reading. To minimize the impact of subsequent filtering steps on the beginning and end of the
signal, an additional 10 seconds of data was retained before the start of the formal reading phase. Subsequently, the signal was
downsampled to 256 Hz.
Following this, a 50 Hz notch filter was applied to remove the powerline noise from the signal. Next, we performed
band-pass overlap-add FIR filter on the signal to eliminate the low-frequency direct current components and high-frequency
noise. Here, two versions of filtered data were offered. The first one has a filter band of 0.5-80 Hz and the second one has
a filter band of 0.5-30 Hz. Researchers can choose the appropriate version based on their specific needs. After filtering, we
performed an interpolation of bad channels. 
Independent Component Analysis (ICA) was then applied to the data, utilizing the infomax algorithm. The number of independent components was set to 20, ensuring that they contain the majority of information while not being so numerous to increase the burden of manual processing. We excluded obvious noise components such as Electrooculography (EOG) and Electrocardiogram (ECG). Finally, the data was re-referenced using the average method. 

The detailed information of the pre-processing can be found in our paper at https://doi.org/10.1101/2024.02.08.579481.

### Text Embeddings

The dataset provides embeddings of two novels calculated using a pre-trained language model BERT-base-Chinese.  During the experimental procedure, each displayed line of text contains n Chinese characters. The BERT-base-Chinese model processes these n Chinese characters, yielding an embedding of size (n, 768), where n represents the number of Chinese characters, and 768 the dimensionality of the embedding. To ensure displayed lines of varying length to have embeddings of the same shape, the first dimension of the embeddings is averaged to standardize the embedding size to (1, 768) for each instance. 

### Missing Data

﻿Due to technical reasons, there are some raw data lost:

- EEG:

  - Sub-09 ses-LittlePrince run 1-3

  - Sub-14 ses-GranettDream run 9

  - Sub-15 ses-GranettDream run 12

  - Sub-07 ses-GranettDream run 18 (substituted by run 19)
      Notice: Sub-07 ses-GranettDream run 19 read chapter 19 of GranettDream instead of chapter 18.


- Eyetracking data:
  - Sub-08 ses-LittlePrince run 1-2




Due to bad quality of data or other reasons, there are some pre-processed data lost:

- In the 0.5-30 Hz filtered version:

  - Sub-15 ses-LittlePrince run 1-7

  - Sub-15 ses-GranettDream run 1, 2, 3, 7, 10, 16


- In the 0.5-80 Hz filtered version:

  - Sub-13 ses-LittlePrince run 4, 5

  - Sub-15 ses-LittlePrince run 1-7

  - Sub-13 ses-GranettDream run 14

  - Sub-15 ses-GranettDream run 1, 2, 3, 7, 10, 16


## Usage Note

If you want to know more about the dataset, including the detailed parameter settings in our pre-processing steps or how to align the text with EEG segments, please refer to our paper at https://doi.org/10.1101/2024.02.08.579481. You can find the relevant code for the text presentation, data processing and other functions at the GitHub repository: https://github.com/ncclabsustech/Chinese_reading_task_eeg_processing.

References
----------

Appelhoff, S., Sanderson, M., Brooks, T., Vliet, M., Quentin, R., Holdgraf, C., Chaumon, M., Mikulan, E., Tavabi, K., Höchenberger, R., Welke, D., Brunner, C., Rockhill, A., Larson, E., Gramfort, A. and Jas, M. (2019). MNE-BIDS: Organizing electrophysiological data into the BIDS format and facilitating their analysis. Journal of Open Source Software 4: (1896). https://doi.org/10.21105/joss.01896

Pernet, C. R., Appelhoff, S., Gorgolewski, K. J., Flandin, G., Phillips, C., Delorme, A., Oostenveld, R. (2019). EEG-BIDS, an extension to the brain imaging data structure for electroencephalography. Scientific Data, 6, 103. https://doi.org/10.1038/s41597-019-0104-8

