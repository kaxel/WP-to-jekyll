---
layout: blog
title: Ruby export to excel walkthrough
permalink: 2008/10/ruby-export-to-excel-walkthrough
categories: [Code]
---

<p>Just write the headers, pull up the xml by report_id, and slurp it out. Piece of cake.</p>
<p>    def export<br />
      headers[&#039;Content-Type&#039;] = "application/vnd.ms-excel"<br />
      headers[&#039;Content-Disposition&#039;] = "attachment; filename=&#039;report.xls&#039;"<br />
      headers[&#039;Cache-Control&#039;] = &#039;&#039;<br />
      s = UserReport.find(params[:report_id])<br />
      col2 = s.col2<br />
      @records = XmlSimple.xml_in(s.xml_val)<br />
      movedir = "public/delivery/"<br />
      x_axis = "Timeline"<br />
      y_axis = @records["yAxisName"] ? @records["yAxisName"] : "$$$"<br />
      f_name = s.file_name<br />
      File.open(f_name, &#039;w&#039;) do |f|<br />
        if current_user.is_test?<br />
          f.puts "Test Data"<br />
        else<br />
          f.puts "#{s.caption}"<br />
        end<br />
        f.puts "#{s.date_range}"<br />
        if s.sub_caption then f.puts "#{s.sub_caption}" end<br />
        f.puts "#{s.col1}#{s.col2}"<br />
        @records["set"].each do |x|<br />
          f.puts "#{clean_col(x["label"])}#{parse_report_value(x["value"], col2)}"<br />
        end<br />
        f.puts "#{s.filter_info}"<br />
        f.puts ""<br />
        f.puts ""<br />
      end<br />
      small_export ="delivery/#{f_name}"<br />
      export_link = "#{movedir}/#{f_name}"<br />
      FileUtils.mkpath(movedir)<br />
      FileUtils.mv f_name, export_link<br />
      small_export<br />
    end</p>