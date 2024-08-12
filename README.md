# camarilla-
book:https:https://book.mql4.com/
//+------------------------------------------------------------------+
//|                                                          com.mq4 |
//|                        Copyright 2024, MetaQuotes Software Corp. |
//|                                             https://www.mql5.com |
//+------------------------------------------------------------------+
#property copyright "Traders Training"
#property link      "https://www.mql5.com"
#property version   "2.00"
#include <stdlib.mqh>
#property strict
#property indicator_buffers 4
#property indicator_color1  clrLimeGreen
#property indicator_color2  clrOrange
#property indicator_color3  clrGray
#property indicator_color4  clrGray
#property indicator_width1  2
#property indicator_width2  2
#property indicator_width3  1
#property indicator_width4  1
#property strict
#property indicator_chart_window
#include <ChartObjects\ChartObjectsTxtControls.mqh> 

//+----------------------------------------+
//|  Input datas                           |
//+----------------------------------------+
input color ResistanceColor = Red;
input color SupportColor = Green;
input int LineWidth = 1;
input ENUM_LINE_STYLE LineStyle = STYLE_SOLID;

extern color CamFontColor = clrYellow;       //Text color
extern int CamFontSize = 10;                //
extern bool CamTargets = true;
extern bool ListCamTargets = true;
extern bool StandardPivots = true;
extern bool ListStandardPivots = true;
extern double  GMTshiftSun=0;  
extern double  GMTshift=1;
extern bool Pivot = true;
extern bool MidPivots = false;
extern color PivotColor = LimeGreen;
extern color PivotFontColor = White;
extern int PivotFontSize = 8;
extern int PipDistance = 20;
extern ENUM_ALIGN_MODE  angle=ALIGN_CENTER;
extern double  Percent      = 20;               // Percent to occupy on the main chart
extern double  PercentShift = 0;                // Percent to vertically shift on the main chart
extern string  UniqueID     = "VolumeOnChart1"; // Unique ID
extern color   NameColor    = clrGray;          // Name color
extern int     NameXPos     = 20;               // Name display X position
extern int     NameYPos     = 20;               // Name display Y position



//+-----------------------------------------+
//|  Global variables to hold pivot points  |             
//+-----------------------------------------+
double H1,H2, H3, H4,H5;
double L1, L2, L3, L4,L5;
double P;
double Q=0,S=0,R=0,M2=0,M3=0,S1=0,R1=0,M1=0,M4=0,S2=0,R2=0,M0=0,M5=0,S3=0,R3=0,nQ=0,nD=0,D=0,R4=0,S4=0,R5=0,S5=0;

double yesterday_high=0;
double yesterday_low=0;
double yesterday_close=0;

double D1=0.091667;
double D2=0.183333;
double D3=0.2750;
double D4=0.55;



double valuehu[],valuehnu[],valuehnd[],valuehd[],vol[];
string shortName;
CChartObjectLabel  label;

double closeprice = Close[0];



//+-----------------------------------------+
//|  Script program start function          |             
//+-----------------------------------------+
int start()
{
// start varibel ...
int  counted_bars=IndicatorCounted();
bool SellAlertTriggered = false;
bool BuyAlertTriggered = false;
double price = iClose(NULL, 0, 0);  // Current close price

CalculateCamarillaPivots();
cam();  // cam line and name call fuction....
volume();  //   volume line and name call fuction....

 if (Pivot)
   {
      if(ObjectFind("P label") != 0)
      {
      ObjectCreate("P label", OBJ_TEXT, 0, Time[0], P);
      ObjectSetText("P label", "Pivot", PivotFontSize, "Arial", PivotFontColor);
      }
      else
      {
      ObjectMove("P label", 0, Time[0], P);
      }

//---  Draw  Pivot lines on chart

      if(ObjectFind("P line") != 0)
      {
      ObjectCreate("P line", OBJ_HLINE, 0, Time[40], P);
      ObjectSet("P line", OBJPROP_STYLE, STYLE_DASH);
      ObjectSet("P line", OBJPROP_COLOR, PivotColor);
      }
      else
      {
      ObjectMove("P line", 0, Time[40], P);
      }

  }
    // Check for Sell Alert (Above H3)
    if (price > H3 && !SellAlertTriggered)
    {
        Alert("Sell Alert: Price crossed above H3 level at ", price);
        SellAlertTriggered = true;  // Prevent repeated alerts
        BuyAlertTriggered = false;  // Reset buy alert
    }
    
    // Check for Buy Alert (Below L3)
    if (price < L3 && !BuyAlertTriggered)
    {
        Alert("Buy Alert: Price crossed below L3 level at ", price);
        BuyAlertTriggered = true;  // Prevent repeated alerts
        SellAlertTriggered = false;  // Reset sell alert
    }
return(0);
}


//+-----------------------------------------+
//|  Script program cam function      |             
//+-----------------------------------------+
void CalculateCamarillaPivots()
{
   double High = iHigh(NULL, PERIOD_D1, 1);
   double Low = iLow(NULL, PERIOD_D1, 1);
   double Close = iClose(NULL, PERIOD_D1, 1);
if (StandardPivots)
{
R5 =(High/Low)*Close;
R4 = Close + (High - Low) * 1.1 / 2;
R3 = Close + (High - Low) * 1.1 / 4;
R2 =  Close + (High - Low) * 1.1 / 6;
R1 = Close + (High - Low) * 1.1 / 12;

S1 = Close + (High - Low) * 1.1 / 12;
S2 = Close - (High - Low) * 1.1 / 6;
S3 = Close - (High - Low) * 1.1 / 4;
S4 = Close - (High - Low) * 1.1 / 2;
S5 = (4*P)-((4* yesterday_high)-yesterday_low);
}
   
   H4 = Close + (High - Low) * 1.1 / 2;
   H3 = Close + (High - Low) * 1.1 / 4;
   H2 = Close + (High - Low) * 1.1 / 6;
   H1 = Close + (High - Low) * 1.1 / 12;
   L1 = Close + (High - Low) * 1.1 / 12;
   L2 = Close - (High - Low) * 1.1 / 6;
   L3 = Close - (High - Low) * 1.1 / 4;
   L4 = Close - (High - Low) * 1.1 / 2;
}

void cam()
{
if (CamTargets)
{  

    if(ObjectFind("H5 label") != 0)
      {
      string label="H5 label";
      ObjectCreate("H5 label", OBJ_TEXT, 0, Time[20], label);//H5....
      ObjectSetText("H5 label", "H5 ", CamFontSize, "Arial", CamFontColor);
      ;      
       }
      else
      {ObjectMove("H5 label", 0, Time[20], H5);}
      
      if(ObjectFind("H4 label") != 0)//H4...
      {
      ObjectCreate("H4 label", OBJ_TEXT, 0, Time[20], H4);//
      ObjectSetText("H4 label", "Sell Stop", CamFontSize, "Arial", CamFontColor);
      
      }
      else
      {ObjectMove("H4 label", 0, Time[20], H4);}
      
      if(ObjectFind("H3 label") != 0)//H3..
      {
      ObjectCreate("H3 label", OBJ_TEXT, 0, Time[20], H3);
      ObjectSetText("H3 label", " Sell Entry", CamFontSize, "Arial", CamFontColor);
      }
      else
      {ObjectMove("H3 label", 0, Time[20], H3);}
      
      if(ObjectFind("H2 label") != 0)//H2..
      {
      ObjectCreate("H2 label", OBJ_TEXT, 0, Time[20], H2);
      ObjectSetText("H2 label", " ", CamFontSize, "Arial", CamFontColor);
      }
      else
      {ObjectMove("H2 label", 0, Time[20], H2);}//H2...end..
      
      if(ObjectFind("H1 label") != 0)//H1..
      {
      ObjectCreate("H1 label", OBJ_TEXT, 0, Time[20], H1);
      ObjectSetText("H1 label", " ", CamFontSize, "Arial", CamFontColor);
      }
      else
      {ObjectMove("H1 label", 0, Time[20], H1);}  
     // finise...H level ... 
     
     
// start...L Level...
       if(ObjectFind("L1 label") != 0)
      {
      ObjectCreate("L1 label", OBJ_TEXT, 0, Time[20], L1);
      ObjectSetText("L1 label", " ", CamFontSize, "Arial", CamFontColor);
     
      }
      else
      {ObjectMove("L1 label", 0, Time[20], L1);}

      if(ObjectFind("L2 label") != 0)
      {
      ObjectCreate("L2 label", OBJ_TEXT, 0, Time[20], L2);
      ObjectSetText("L2 label", " ", CamFontSize, "Arial", CamFontColor);
      }
      else
      {ObjectMove("L2 label", 0, Time[20], L2);}

      if(ObjectFind("L3 label") != 0)
      {
      ObjectCreate("L3 label", OBJ_TEXT, 0, Time[20], L3);
      ObjectSetText("L3 label", " Buy Entry", CamFontSize, "Arial", CamFontColor);
      }
      else
      {
      ObjectMove("L3 label", 0, Time[20], L3);
      }

      if(ObjectFind("L4 label") != 0)
      {
      ObjectCreate("L4 label", OBJ_TEXT, 0, Time[20], L4);
      ObjectSetText("L4 label", " Buy stop", CamFontSize, "Arial", CamFontColor);
      }
      else
      {
      ObjectMove("L4 label", 0, Time[20], L4);
      }
      
      if(ObjectFind("L5 label") != 0)
      {
      ObjectCreate("L5 label", OBJ_TEXT, 0, Time[20], L5);
      ObjectSetText("L5 label", " ", CamFontSize, "Arial", CamFontColor);
      }
      else
      {
      ObjectMove("L5 label", 0, Time[20], L5);
      }

//---- Draw Camarilla lines on Chart
//----Draw H level...line

      if(ObjectFind("H5 line") != 0)
      {
      ObjectCreate("H5 line", OBJ_HLINE, 0, Time[40], H5);
      ObjectSet("H5 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("H5 line", OBJPROP_COLOR, SpringGreen);
      ObjectSet("H5 line", OBJPROP_WIDTH, 1);
       }
      else
      {ObjectMove("H5 line", 0, Time[40], H5);}
      
      if(ObjectFind("H4 line") != 0)
      {
      ObjectCreate("H4 line", OBJ_HLINE, 0, Time[40], H4);
      ObjectSet("H4 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("H4 line", OBJPROP_COLOR, SpringGreen);
      ObjectSet("H4 line", OBJPROP_WIDTH, 1);
      }
      else
      {ObjectMove("H4 line", 0, Time[40], H4);}

      if(ObjectFind("H3 line") != 0)
      {
      ObjectCreate("H3 line", OBJ_HLINE, 0, Time[40], H3);
      ObjectSet("H3 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("H3 line", OBJPROP_COLOR, SpringGreen);
      ObjectSet("H3 line", OBJPROP_WIDTH, 1);
      }
      else
      {ObjectMove("H3 line", 0, Time[40], H3);}
      
      if(ObjectFind("H2 line") != 0)
      {
      ObjectCreate("H2 line", OBJ_HLINE, 0, Time[40], H2);
      ObjectSet("H2 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("H2 line", OBJPROP_COLOR, SpringGreen);
      ObjectSet("H2 line", OBJPROP_WIDTH, 1);
      }
      else
      {ObjectMove("H2 line", 0, Time[40], H2);}

      if(ObjectFind("H1 line") != 0)
      {
      ObjectCreate("H1 line", OBJ_HLINE, 0, Time[40], H1);
      ObjectSet("H1 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("H1 line", OBJPROP_COLOR, SpringGreen);
      ObjectSet("H1 line", OBJPROP_WIDTH, 1);
      }
      else
      {ObjectMove("H1 line", 0, Time[40], H1);}      
      

 //---Draw L.. level line...
      if(ObjectFind("L1 line") != 0)
      {
      ObjectCreate("L1 line", OBJ_HLINE, 0, Time[40], L1);
      ObjectSet("L1 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("L1 line", OBJPROP_COLOR, Red);
      ObjectSet("L1 line", OBJPROP_WIDTH, 1);
      }
      else
      {ObjectMove("L1 line", 0, Time[40], L1);}
 
      if(ObjectFind("L2 line") != 0)
      {
      ObjectCreate("L2 line", OBJ_HLINE, 0, Time[40], L2);
      ObjectSet("L2 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("L2 line", OBJPROP_COLOR, Red);
      ObjectSet("L2 line", OBJPROP_WIDTH, 1);
      }
      else
      {ObjectMove("L2 line", 0, Time[40], L2);}
 
      if(ObjectFind("L3 line") != 0)
      {
      ObjectCreate("L3 line", OBJ_HLINE, 0, Time[40], L3);
      ObjectSet("L3 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("L3 line", OBJPROP_COLOR, Red);
      ObjectSet("L3 line", OBJPROP_WIDTH, 1);
      }
      else
      {ObjectMove("L3 line", 0, Time[40], L3);}

      if(ObjectFind("L4 line") != 0)
      {
      ObjectCreate("L4 line", OBJ_HLINE, 0, Time[40], L4);
      ObjectSet("L4 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("L4 line", OBJPROP_COLOR, Red);
      ObjectSet("L4 line", OBJPROP_WIDTH, 1);
      }
      else
      {ObjectMove("L4 line", 0, Time[40], L4);}
      
      if(ObjectFind("L5 line") != 0)
      {
      ObjectCreate("L5 line", OBJ_HLINE, 0, Time[40], L5);
      ObjectSet("L5 line", OBJPROP_STYLE, STYLE_SOLID);
      ObjectSet("L5 line", OBJPROP_COLOR, Red);
      ObjectSet("L5 line", OBJPROP_WIDTH, 1);
      }
      else
      {
      ObjectMove("L5 line", 0, Time[40], L5);
      }
}
}

//+-----------------------------------------+
//|  Script program Deinit function         |             
//+-----------------------------------------+
int OnDeinit()
{
  if(Pivot=True)
   {
     ObjectDelete("P Label");
     ObjectDelete("P Line"); 
   }
  if(CamTargets=True)
   {
     ObjectDelete("H1");
     ObjectDelete("H2");
     ObjectDelete("H3");
     ObjectDelete("H4");
     ObjectDelete("L1");
     ObjectDelete("L2");
     ObjectDelete("L3");
     ObjectDelete("L4");    
   }
   return(0);
}
////
void volume()
{
int counted_bars=IndicatorCounted();
      if(counted_bars<0) ;
      if(counted_bars>0) counted_bars--;
         int bars  = (int)MathMin(ChartGetInteger(0,CHART_WIDTH_IN_BARS),Bars-2);
         int limit =      MathMax(MathMin(Bars-counted_bars,Bars-1),bars);
         double chartMax = ChartGetDouble(0,CHART_PRICE_MAX);
         double chartMin = ChartGetDouble(0,CHART_PRICE_MIN);
         double mod      = Percent*(chartMax-chartMin)/100.0;
      for(int i=limit; i>=0; i--)
      {
         vol[i] = (double)Volume[i];
            valuehu[i]  = (Close[i]>Open[i])  ? (double)Volume[i] : 0;
            valuehd[i]  = (Close[i]<Open[i])  ? (double)Volume[i] : 0;
            valuehnu[i] = (Close[i]==Open[i]) ? (double)Volume[i] : 0;
            valuehnd[i] = 0;
      }         
      label.Description(shortName+" : "+DoubleToStr(Volume[0],0));
 
      double min = 0;
      double max = vol[ArrayMaximum(vol,bars,0)];
      double rng = max-min; chartMin = chartMin+PercentShift*(chartMax-chartMin)/100.0;
      for(int i=bars; i>=0; i--)
      {
         valuehu[i]  = chartMin+(valuehu[i] -min)/rng*mod;
         valuehd[i]  = chartMin+(valuehd[i] -min)/rng*mod;
         valuehnu[i] = chartMin+(valuehnu[i]-min)/rng*mod;
         valuehnd[i] = chartMin;
      }
      for (int i=0; i<2; i++) SetIndexDrawBegin(i,Bars-bars+1);
    
}
//
int init()
{
   IndicatorBuffers(5);
   SetIndexBuffer(0,valuehu);  SetIndexStyle(0,DRAW_HISTOGRAM);
   SetIndexBuffer(1,valuehd);  SetIndexStyle(1,DRAW_HISTOGRAM);
   SetIndexBuffer(2,valuehnu); SetIndexStyle(2,DRAW_HISTOGRAM);
   SetIndexBuffer(3,valuehnd); SetIndexStyle(3,DRAW_HISTOGRAM);
   SetIndexBuffer(4,vol); 
            
   shortName = "Volumes on chart";
   IndicatorShortName(shortName);
      label.Create(0,UniqueID+":name",0,NameXPos,NameYPos);
      label.Color(NameColor);
      label.Description(shortName);
      label.FontSize(7); 
      label.Font("Verdana"); 
      label.Corner(CORNER_RIGHT_LOWER);
      label.Anchor(ANCHOR_RIGHT);
         for (int i=0; i<2; i++) SetIndexLabel(i,shortName);
   return(0);
}
int deinit() { ObjectDelete(UniqueID+":name"); return(0); }
