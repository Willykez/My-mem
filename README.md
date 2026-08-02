# My-mem
mem
// ── Tier A #3: Share result as styled image ───────────────────────────────
    fun setShareBitmap(bmp: android.graphics.Bitmap) =
        _state.update { it.copy(shareImageBitmap = bmp, showShareSheet = true) }
    fun clearShareBitmap() =
        _state.update { it.copy(shareImageBitmap = null, showShareSheet = false) }

    // ── Tier B #10: Export history as CSV ────────────────────────────────────
    fun exportHistoryAsCsv(context: android.content.Context) {
        viewModelScope.launch {
            try {
                val history = _state.value.history
                if (history.isEmpty()) { _state.update { it.copy(toastMessage = "No history to export") }; return@launch }
                val ts  = java.text.SimpleDateFormat("dd-MM-yyyy-HH-mm-ss", java.util.Locale.getDefault()).format(java.util.Date())
                val dir = java.io.File(android.os.Environment.getExternalStoragePublicDirectory(
                    android.os.Environment.DIRECTORY_DOCUMENTS), "NidaInfo").also { it.mkdirs() }
                val f   = java.io.File(dir, "NidaHistory_$ts.csv")
                java.io.FileWriter(f).use { w ->
                    w.appendLine("NIN,Name,Question Code,Answer,Timestamp")
                    history.forEach { e ->
                        w.appendLine(""${e.nin}","${e.name}","${e.questionCode}","${e.answer}","${e.timestamp}"")
                    }
                }
                val uri = androidx.core.content.FileProvider.getUriForFile(context, "${context.packageName}.provider", f)
                val intent = android.content.Intent(android.content.Intent.ACTION_VIEW).apply {
                    setDataAndType(uri, "text/csv")
                    addFlags(android.content.Intent.FLAG_GRANT_READ_URI_PERMISSION or android.content.Intent.FLAG_ACTIVITY_NEW_TASK)
                }
                context.startActivity(intent)
                _state.update { it.copy(toastMessage = "✓ History exported to Documents/NidaInfo") }
            } catch (e: Exception) {
                _state.update { it.copy(toastMessage = "Export failed: ${e.message}") }
            }
        }
    }w.appendLine(
    """"${e.nin}","${e.name}","${e.questionCode}","${e.answer}","${e.timestamp}""""
)https://youtube.com/shorts/Li2poownnSs?si=UGDGczl0AOak_6_v